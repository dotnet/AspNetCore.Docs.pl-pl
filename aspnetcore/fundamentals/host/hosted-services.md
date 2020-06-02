---
<span data-ttu-id="55eab-101">title: zadania w tle z usługami hostowanymi w ASP.NET Core Author: Rick-Anderson Description: informacje na temat implementowania zadań w tle za pomocą usług hostowanych w programie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="55eab-101">title: Background tasks with hosted services in ASP.NET Core author: rick-anderson description: Learn how to implement background tasks with hosted services in ASP.NET Core.</span></span>
<span data-ttu-id="55eab-102">monikerRange: ">= aspnetcore-2,1" MS. Author: Riande MS. Custom: MVC MS. Date: 02/10/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="55eab-102">monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 02/10/2020 no-loc:</span></span>
- <span data-ttu-id="55eab-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="55eab-103">'Blazor'</span></span>
- <span data-ttu-id="55eab-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="55eab-104">'Identity'</span></span>
- <span data-ttu-id="55eab-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="55eab-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="55eab-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="55eab-106">'Razor'</span></span>
- <span data-ttu-id="55eab-107">" SignalR UID: podstawy/Host/usługi hostowane</span><span class="sxs-lookup"><span data-stu-id="55eab-107">'SignalR' uid: fundamentals/host/hosted-services</span></span>

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="55eab-108">Zadania w tle z usługami hostowanymi w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55eab-108">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="55eab-109">Według [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="55eab-109">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="55eab-110">W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*.</span><span class="sxs-lookup"><span data-stu-id="55eab-110">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="55eab-111">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="55eab-111">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="55eab-112">Ten temat zawiera trzy przykłady usługi hostowanej:</span><span class="sxs-lookup"><span data-stu-id="55eab-112">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="55eab-113">Zadanie w tle, które jest uruchamiane na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="55eab-113">Background task that runs on a timer.</span></span>
* <span data-ttu-id="55eab-114">Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="55eab-114">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="55eab-115">Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="55eab-115">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="55eab-116">Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="55eab-116">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="55eab-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55eab-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="55eab-118">Szablon usługi procesu roboczego</span><span class="sxs-lookup"><span data-stu-id="55eab-118">Worker Service template</span></span>

<span data-ttu-id="55eab-119">Szablon usługi ASP.NET Core Worker zapewnia punkt początkowy do pisania długotrwałych aplikacji usługi.</span><span class="sxs-lookup"><span data-stu-id="55eab-119">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="55eab-120">Aplikacja utworzona na podstawie szablonu usługi procesu roboczego określa zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="55eab-120">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="55eab-121">Aby użyć szablonu jako podstawy dla aplikacji usług hostowanych:</span><span class="sxs-lookup"><span data-stu-id="55eab-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="55eab-122">Pakiet</span><span class="sxs-lookup"><span data-stu-id="55eab-122">Package</span></span>

<span data-ttu-id="55eab-123">Aplikacja oparta na szablonie usługi procesu roboczego używa `Microsoft.NET.Sdk.Worker` zestawu SDK i ma jawne odwołanie do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="55eab-123">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="55eab-124">Na przykład zapoznaj się z plikiem projektu przykładowej aplikacji (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="55eab-124">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="55eab-125">W przypadku aplikacji sieci Web, które używają `Microsoft.NET.Sdk.Web` zestawu SDK, pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) jest przywoływany niejawnie z udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="55eab-125">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="55eab-126">Jawne odwołanie do pakietu w pliku projektu aplikacji nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="55eab-126">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="55eab-127">IHostedService, interfejs</span><span class="sxs-lookup"><span data-stu-id="55eab-127">IHostedService interface</span></span>

<span data-ttu-id="55eab-128"><xref:Microsoft.Extensions.Hosting.IHostedService>Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:</span><span class="sxs-lookup"><span data-stu-id="55eab-128">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="55eab-129">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="55eab-130">`StartAsync`jest wywoływana *przed*:</span><span class="sxs-lookup"><span data-stu-id="55eab-130">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="55eab-131">Skonfigurowano potok przetwarzania żądania aplikacji ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="55eab-131">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="55eab-132">Serwer został uruchomiony i zostanie wyzwolony [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="55eab-132">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="55eab-133">Zachowanie domyślne można zmienić tak, aby usługa hostowana `StartAsync` działała po skonfigurowaniu potoku aplikacji i `ApplicationStarted` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="55eab-133">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="55eab-134">Aby zmienić zachowanie domyślne, należy dodać usługę hostowaną ( `VideosWatcher` w poniższym przykładzie) po wywołaniu `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="55eab-134">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="55eab-135">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="55eab-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="55eab-136">`StopAsync`zawiera logikę końcową zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-136">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="55eab-137">Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="55eab-137">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="55eab-138">Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny.</span><span class="sxs-lookup"><span data-stu-id="55eab-138">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="55eab-139">Gdy żądanie anulowania jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="55eab-139">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="55eab-140">Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.</span><span class="sxs-lookup"><span data-stu-id="55eab-140">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="55eab-141">Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.</span><span class="sxs-lookup"><span data-stu-id="55eab-141">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="55eab-142">Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.</span><span class="sxs-lookup"><span data-stu-id="55eab-142">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="55eab-143">Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany.</span><span class="sxs-lookup"><span data-stu-id="55eab-143">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="55eab-144">W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="55eab-144">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="55eab-145">Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:</span><span class="sxs-lookup"><span data-stu-id="55eab-145">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="55eab-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>w przypadku korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="55eab-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="55eab-147">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="55eab-147">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="55eab-148">Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="55eab-148">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="55eab-149">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="55eab-149">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="55eab-150">Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55eab-150">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="55eab-151">Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="55eab-151">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="55eab-152">Klasa bazowa BackgroundService</span><span class="sxs-lookup"><span data-stu-id="55eab-152">BackgroundService base class</span></span>

<span data-ttu-id="55eab-153"><xref:Microsoft.Extensions.Hosting.BackgroundService>jest klasą bazową do implementowania długotrwałego działania <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="55eab-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="55eab-154">[Wywoływanie ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) jest wywoływana w celu uruchomienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="55eab-155">Implementacja zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą cały okres istnienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-155">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="55eab-156">Żadne dalsze usługi nie są uruchamiane do momentu, gdy [wywoływanie ExecuteAsync staną się asynchroniczne](https://github.com/dotnet/extensions/issues/2149), na przykład przez wywołanie `await` .</span><span class="sxs-lookup"><span data-stu-id="55eab-156">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="55eab-157">Unikaj długotrwałych operacji inicjalizacji `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="55eab-157">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="55eab-158">Bloki hosta w [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) oczekują na `ExecuteAsync` zakończenie.</span><span class="sxs-lookup"><span data-stu-id="55eab-158">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="55eab-159">Token anulowania jest wyzwalany, gdy wywoływana jest [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="55eab-159">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="55eab-160">Implementacja programu `ExecuteAsync` powinna zakończyć się natychmiast po uruchomieniu tokenu anulowania w celu łagodnego zamknięcia usługi.</span><span class="sxs-lookup"><span data-stu-id="55eab-160">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="55eab-161">W przeciwnym razie usługa niebezpiecznie zamyka się po upływie limitu czasu zamykania.</span><span class="sxs-lookup"><span data-stu-id="55eab-161">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="55eab-162">Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [interfejsu IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="55eab-162">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="55eab-163">Zadania w tle czasu</span><span class="sxs-lookup"><span data-stu-id="55eab-163">Timed background tasks</span></span>

<span data-ttu-id="55eab-164">Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="55eab-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="55eab-165">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="55eab-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="55eab-166">Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="55eab-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="55eab-167"><xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="55eab-167">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="55eab-168">Z [blokadą. przyrost](xref:System.Threading.Interlocked.Increment*) służy do zwiększania licznika wykonywania jako operacja niepodzielna, która zapewnia, że wiele wątków nie zostanie jednocześnie zaktualizowanych `executionCount` .</span><span class="sxs-lookup"><span data-stu-id="55eab-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="55eab-169">Usługa jest zarejestrowana w `IHostBuilder.ConfigureServices` (*program.cs*) z `AddHostedService` metodą rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="55eab-169">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="55eab-170">Zużywanie usługi w zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="55eab-170">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="55eab-171">Aby użyć [usług z zakresem](xref:fundamentals/dependency-injection#service-lifetimes) w ramach [BackgroundService](#backgroundservice-base-class), Utwórz zakres.</span><span class="sxs-lookup"><span data-stu-id="55eab-171">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="55eab-172">Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.</span><span class="sxs-lookup"><span data-stu-id="55eab-172">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="55eab-173">Usługa zadań w tle w zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-173">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="55eab-174">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="55eab-174">In the following example:</span></span>

* <span data-ttu-id="55eab-175">Usługa jest asynchroniczna.</span><span class="sxs-lookup"><span data-stu-id="55eab-175">The service is asynchronous.</span></span> <span data-ttu-id="55eab-176">`DoWork`Metoda zwraca `Task` .</span><span class="sxs-lookup"><span data-stu-id="55eab-176">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="55eab-177">W celach demonstracyjnych oczekuje się, że w metodzie występuje opóźnienie o dziesięć sekund `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="55eab-177">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="55eab-178"><xref:Microsoft.Extensions.Logging.ILogger>Jest wstrzykiwana do usługi.</span><span class="sxs-lookup"><span data-stu-id="55eab-178">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="55eab-179">Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie, aby wywołać `DoWork` metodę.</span><span class="sxs-lookup"><span data-stu-id="55eab-179">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="55eab-180">`DoWork`Zwraca element `Task` , który jest oczekiwany w `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="55eab-180">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="55eab-181">Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="55eab-181">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="55eab-182">Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="55eab-182">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="55eab-183">Zadania w kolejce w dół</span><span class="sxs-lookup"><span data-stu-id="55eab-183">Queued background tasks</span></span>

<span data-ttu-id="55eab-184">Kolejka zadań w tle jest oparta na platformie .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :</span><span class="sxs-lookup"><span data-stu-id="55eab-184">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="55eab-185">W poniższym `QueueHostedService` przykładzie:</span><span class="sxs-lookup"><span data-stu-id="55eab-185">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="55eab-186">`BackgroundProcessing`Metoda zwraca obiekt `Task` , który jest oczekiwany w `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="55eab-186">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="55eab-187">Zadania w tle w kolejce są dekolejkowane i wykonywane w `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="55eab-187">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="55eab-188">Elementy robocze oczekują na zatrzymanie usługi w usłudze `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="55eab-188">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="55eab-189">`MonitorLoop`Usługa obsługuje zadania umieszczenie dla usługi hostowanej za każdym razem, gdy `w` klucz zostanie wybrany na urządzeniu wejściowym:</span><span class="sxs-lookup"><span data-stu-id="55eab-189">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="55eab-190">`IBackgroundTaskQueue`Jest wstrzykiwana do `MonitorLoop` usługi.</span><span class="sxs-lookup"><span data-stu-id="55eab-190">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="55eab-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem`jest wywoływana w celu dodawania do kolejki elementu pracy.</span><span class="sxs-lookup"><span data-stu-id="55eab-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="55eab-192">Element roboczy symuluje długotrwałe zadanie w tle:</span><span class="sxs-lookup"><span data-stu-id="55eab-192">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="55eab-193">Trzy 5-sekundowe opóźnienia są wykonywane ( `Task.Delay` ).</span><span class="sxs-lookup"><span data-stu-id="55eab-193">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="55eab-194">`try-catch`Pułapka instrukcji <xref:System.OperationCanceledException> , jeśli zadanie zostało anulowane.</span><span class="sxs-lookup"><span data-stu-id="55eab-194">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="55eab-195">Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="55eab-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="55eab-196">Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="55eab-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="55eab-197">`MonitorLoop`jest uruchamiany w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="55eab-197">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="55eab-198">W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*.</span><span class="sxs-lookup"><span data-stu-id="55eab-198">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="55eab-199">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="55eab-199">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="55eab-200">Ten temat zawiera trzy przykłady usługi hostowanej:</span><span class="sxs-lookup"><span data-stu-id="55eab-200">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="55eab-201">Zadanie w tle, które jest uruchamiane na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="55eab-201">Background task that runs on a timer.</span></span>
* <span data-ttu-id="55eab-202">Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="55eab-202">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="55eab-203">Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="55eab-203">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="55eab-204">Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="55eab-204">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="55eab-205">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55eab-205">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="55eab-206">Pakiet</span><span class="sxs-lookup"><span data-stu-id="55eab-206">Package</span></span>

<span data-ttu-id="55eab-207">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="55eab-207">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="55eab-208">IHostedService, interfejs</span><span class="sxs-lookup"><span data-stu-id="55eab-208">IHostedService interface</span></span>

<span data-ttu-id="55eab-209">Usługi hostowane implementują <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="55eab-209">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="55eab-210">Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:</span><span class="sxs-lookup"><span data-stu-id="55eab-210">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="55eab-211">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="55eab-212">W przypadku korzystania z [hosta sieci Web](xref:fundamentals/host/web-host) `StartAsync` jest wywoływana po uruchomieniu serwera i wyzwoleniu [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="55eab-212">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="55eab-213">W przypadku korzystania z [hosta generycznego](xref:fundamentals/host/generic-host) `StartAsync` jest wywoływana przed wywołaniem `ApplicationStarted` .</span><span class="sxs-lookup"><span data-stu-id="55eab-213">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="55eab-214">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="55eab-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="55eab-215">`StopAsync`zawiera logikę końcową zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-215">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="55eab-216">Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="55eab-216">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="55eab-217">Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny.</span><span class="sxs-lookup"><span data-stu-id="55eab-217">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="55eab-218">Gdy żądanie anulowania jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="55eab-218">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="55eab-219">Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.</span><span class="sxs-lookup"><span data-stu-id="55eab-219">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="55eab-220">Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.</span><span class="sxs-lookup"><span data-stu-id="55eab-220">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="55eab-221">Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.</span><span class="sxs-lookup"><span data-stu-id="55eab-221">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="55eab-222">Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany.</span><span class="sxs-lookup"><span data-stu-id="55eab-222">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="55eab-223">W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="55eab-223">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="55eab-224">Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:</span><span class="sxs-lookup"><span data-stu-id="55eab-224">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="55eab-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>w przypadku korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="55eab-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="55eab-226">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="55eab-226">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="55eab-227">Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="55eab-227">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="55eab-228">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="55eab-228">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="55eab-229">Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55eab-229">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="55eab-230">Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="55eab-230">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="55eab-231">Zadania w tle czasu</span><span class="sxs-lookup"><span data-stu-id="55eab-231">Timed background tasks</span></span>

<span data-ttu-id="55eab-232">Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="55eab-232">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="55eab-233">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="55eab-233">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="55eab-234">Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="55eab-234">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="55eab-235"><xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="55eab-235">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="55eab-236">Usługa jest zarejestrowana w ramach `Startup.ConfigureServices` `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="55eab-236">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="55eab-237">Zużywanie usługi w zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="55eab-237">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="55eab-238">Aby korzystać z usług o określonym [zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w ramach programu `IHostedService` , należy utworzyć zakres.</span><span class="sxs-lookup"><span data-stu-id="55eab-238">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="55eab-239">Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.</span><span class="sxs-lookup"><span data-stu-id="55eab-239">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="55eab-240">Usługa zadań w tle w zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="55eab-240">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="55eab-241">W poniższym przykładzie <xref:Microsoft.Extensions.Logging.ILogger> jest wstrzykiwana do usługi:</span><span class="sxs-lookup"><span data-stu-id="55eab-241">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="55eab-242">Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie w celu wywołania jej `DoWork` metody:</span><span class="sxs-lookup"><span data-stu-id="55eab-242">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="55eab-243">Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="55eab-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55eab-244">`IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="55eab-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="55eab-245">Zadania w kolejce w dół</span><span class="sxs-lookup"><span data-stu-id="55eab-245">Queued background tasks</span></span>

<span data-ttu-id="55eab-246">Kolejka zadań w tle jest oparta na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([wstępnie zaplanowano wbudowaną ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="55eab-246">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="55eab-247">W programie `QueueHostedService` zadania w tle w kolejce są odrzucane i wykonywane jako [BackgroundService](#backgroundservice-base-class), która jest klasą bazową do implementowania długotrwałego działania `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="55eab-247">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="55eab-248">Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="55eab-248">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55eab-249">`IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="55eab-249">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="55eab-250">W klasie modelu strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="55eab-250">In the Index page model class:</span></span>

* <span data-ttu-id="55eab-251">`IBackgroundTaskQueue`Zostaje wstrzyknięty do konstruktora i przypisany do `Queue` .</span><span class="sxs-lookup"><span data-stu-id="55eab-251">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="55eab-252"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Jest wstrzykiwany i przypisany do `_serviceScopeFactory` .</span><span class="sxs-lookup"><span data-stu-id="55eab-252">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="55eab-253">Fabryka służy do tworzenia wystąpień <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> , które są używane do tworzenia usług w zakresie.</span><span class="sxs-lookup"><span data-stu-id="55eab-253">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="55eab-254">Zakres jest tworzony w celu używania aplikacji `AppDbContext` ( [usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes)) do zapisywania rekordów bazy danych w `IBackgroundTaskQueue` (pojedynczej usłudze).</span><span class="sxs-lookup"><span data-stu-id="55eab-254">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="55eab-255">Po wybraniu przycisku **Dodaj zadanie** na stronie indeks `OnPostAddTask` jest wykonywana metoda.</span><span class="sxs-lookup"><span data-stu-id="55eab-255">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="55eab-256">`QueueBackgroundWorkItem`jest wywoływana w celu dodawania do kolejki elementu pracy:</span><span class="sxs-lookup"><span data-stu-id="55eab-256">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="55eab-257">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="55eab-257">Additional resources</span></span>

* [<span data-ttu-id="55eab-258">Implementowanie zadań w tle w mikrousługach za pomocą IHostedService i klasy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="55eab-258">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="55eab-259">Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="55eab-259">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
