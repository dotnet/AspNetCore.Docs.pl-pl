---
<span data-ttu-id="70cfe-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="70cfe-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="70cfe-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="70cfe-102">'Blazor'</span></span>
- <span data-ttu-id="70cfe-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="70cfe-103">'Identity'</span></span>
- <span data-ttu-id="70cfe-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="70cfe-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="70cfe-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="70cfe-105">'Razor'</span></span>
- <span data-ttu-id="70cfe-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="70cfe-106">'SignalR' uid:</span></span> 

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="70cfe-107">Zadania w tle z usługami hostowanymi w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70cfe-107">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="70cfe-108">Według [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="70cfe-108">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70cfe-109">W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*.</span><span class="sxs-lookup"><span data-stu-id="70cfe-109">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="70cfe-110">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="70cfe-110">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="70cfe-111">Ten temat zawiera trzy przykłady usługi hostowanej:</span><span class="sxs-lookup"><span data-stu-id="70cfe-111">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="70cfe-112">Zadanie w tle, które jest uruchamiane na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="70cfe-112">Background task that runs on a timer.</span></span>
* <span data-ttu-id="70cfe-113">Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-113">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="70cfe-114">Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="70cfe-114">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="70cfe-115">Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-115">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="70cfe-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70cfe-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="70cfe-117">Szablon usługi procesu roboczego</span><span class="sxs-lookup"><span data-stu-id="70cfe-117">Worker Service template</span></span>

<span data-ttu-id="70cfe-118">Szablon usługi ASP.NET Core Worker zapewnia punkt początkowy do pisania długotrwałych aplikacji usługi.</span><span class="sxs-lookup"><span data-stu-id="70cfe-118">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="70cfe-119">Aplikacja utworzona na podstawie szablonu usługi procesu roboczego określa zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="70cfe-119">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="70cfe-120">Aby użyć szablonu jako podstawy dla aplikacji usług hostowanych:</span><span class="sxs-lookup"><span data-stu-id="70cfe-120">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="70cfe-121">Pakiet</span><span class="sxs-lookup"><span data-stu-id="70cfe-121">Package</span></span>

<span data-ttu-id="70cfe-122">Aplikacja oparta na szablonie usługi procesu roboczego używa `Microsoft.NET.Sdk.Worker` zestawu SDK i ma jawne odwołanie do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-122">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="70cfe-123">Na przykład zapoznaj się z plikiem projektu przykładowej aplikacji (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="70cfe-123">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="70cfe-124">W przypadku aplikacji sieci Web, które używają `Microsoft.NET.Sdk.Web` zestawu SDK, pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) jest przywoływany niejawnie z udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="70cfe-124">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="70cfe-125">Jawne odwołanie do pakietu w pliku projektu aplikacji nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="70cfe-125">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="70cfe-126">IHostedService, interfejs</span><span class="sxs-lookup"><span data-stu-id="70cfe-126">IHostedService interface</span></span>

<span data-ttu-id="70cfe-127"><xref:Microsoft.Extensions.Hosting.IHostedService>Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:</span><span class="sxs-lookup"><span data-stu-id="70cfe-127">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="70cfe-128">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-128">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="70cfe-129">`StartAsync`jest wywoływana *przed*:</span><span class="sxs-lookup"><span data-stu-id="70cfe-129">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="70cfe-130">Skonfigurowano potok przetwarzania żądania aplikacji ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="70cfe-130">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="70cfe-131">Serwer został uruchomiony i zostanie wyzwolony [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-131">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="70cfe-132">Zachowanie domyślne można zmienić tak, aby usługa hostowana `StartAsync` działała po skonfigurowaniu potoku aplikacji i `ApplicationStarted` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="70cfe-132">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="70cfe-133">Aby zmienić zachowanie domyślne, należy dodać usługę hostowaną ( `VideosWatcher` w poniższym przykładzie) po wywołaniu `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="70cfe-133">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="70cfe-134">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-134">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="70cfe-135">`StopAsync`zawiera logikę końcową zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-135">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="70cfe-136">Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="70cfe-136">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="70cfe-137">Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny.</span><span class="sxs-lookup"><span data-stu-id="70cfe-137">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="70cfe-138">Gdy żądanie anulowania jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="70cfe-138">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="70cfe-139">Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.</span><span class="sxs-lookup"><span data-stu-id="70cfe-139">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="70cfe-140">Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.</span><span class="sxs-lookup"><span data-stu-id="70cfe-140">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="70cfe-141">Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.</span><span class="sxs-lookup"><span data-stu-id="70cfe-141">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="70cfe-142">Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany.</span><span class="sxs-lookup"><span data-stu-id="70cfe-142">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="70cfe-143">W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="70cfe-143">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="70cfe-144">Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:</span><span class="sxs-lookup"><span data-stu-id="70cfe-144">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="70cfe-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>w przypadku korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="70cfe-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="70cfe-146">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="70cfe-146">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="70cfe-147">Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="70cfe-147">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="70cfe-148">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="70cfe-148">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="70cfe-149">Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="70cfe-149">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="70cfe-150">Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="70cfe-150">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="70cfe-151">Klasa bazowa BackgroundService</span><span class="sxs-lookup"><span data-stu-id="70cfe-151">BackgroundService base class</span></span>

<span data-ttu-id="70cfe-152"><xref:Microsoft.Extensions.Hosting.BackgroundService>jest klasą bazową do implementowania długotrwałego działania <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="70cfe-152"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="70cfe-153">[Wywoływanie ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) jest wywoływana w celu uruchomienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-153">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="70cfe-154">Implementacja zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą cały okres istnienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-154">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="70cfe-155">Żadne dalsze usługi nie są uruchamiane do momentu, gdy [wywoływanie ExecuteAsync staną się asynchroniczne](https://github.com/dotnet/extensions/issues/2149), na przykład przez wywołanie `await` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-155">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="70cfe-156">Unikaj długotrwałych operacji inicjalizacji `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-156">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="70cfe-157">Bloki hosta w [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) oczekują na `ExecuteAsync` zakończenie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-157">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="70cfe-158">Token anulowania jest wyzwalany, gdy wywoływana jest [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-158">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="70cfe-159">Implementacja programu `ExecuteAsync` powinna zakończyć się natychmiast po uruchomieniu tokenu anulowania w celu łagodnego zamknięcia usługi.</span><span class="sxs-lookup"><span data-stu-id="70cfe-159">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="70cfe-160">W przeciwnym razie usługa niebezpiecznie zamyka się po upływie limitu czasu zamykania.</span><span class="sxs-lookup"><span data-stu-id="70cfe-160">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="70cfe-161">Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [interfejsu IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-161">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="70cfe-162">Zadania w tle czasu</span><span class="sxs-lookup"><span data-stu-id="70cfe-162">Timed background tasks</span></span>

<span data-ttu-id="70cfe-163">Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-163">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="70cfe-164">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="70cfe-164">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="70cfe-165">Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="70cfe-165">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="70cfe-166"><xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="70cfe-166">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="70cfe-167">Z [blokadą. przyrost](xref:System.Threading.Interlocked.Increment*) służy do zwiększania licznika wykonywania jako operacja niepodzielna, która zapewnia, że wiele wątków nie zostanie jednocześnie zaktualizowanych `executionCount` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-167">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="70cfe-168">Usługa jest zarejestrowana w `IHostBuilder.ConfigureServices` (*program.cs*) z `AddHostedService` metodą rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="70cfe-168">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="70cfe-169">Zużywanie usługi w zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="70cfe-169">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="70cfe-170">Aby użyć [usług z zakresem](xref:fundamentals/dependency-injection#service-lifetimes) w ramach [BackgroundService](#backgroundservice-base-class), Utwórz zakres.</span><span class="sxs-lookup"><span data-stu-id="70cfe-170">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="70cfe-171">Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.</span><span class="sxs-lookup"><span data-stu-id="70cfe-171">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="70cfe-172">Usługa zadań w tle w zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-172">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="70cfe-173">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="70cfe-173">In the following example:</span></span>

* <span data-ttu-id="70cfe-174">Usługa jest asynchroniczna.</span><span class="sxs-lookup"><span data-stu-id="70cfe-174">The service is asynchronous.</span></span> <span data-ttu-id="70cfe-175">`DoWork`Metoda zwraca `Task` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-175">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="70cfe-176">W celach demonstracyjnych oczekuje się, że w metodzie występuje opóźnienie o dziesięć sekund `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-176">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="70cfe-177"><xref:Microsoft.Extensions.Logging.ILogger>Jest wstrzykiwana do usługi.</span><span class="sxs-lookup"><span data-stu-id="70cfe-177">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="70cfe-178">Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie, aby wywołać `DoWork` metodę.</span><span class="sxs-lookup"><span data-stu-id="70cfe-178">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="70cfe-179">`DoWork`Zwraca element `Task` , który jest oczekiwany w `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="70cfe-179">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="70cfe-180">Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="70cfe-180">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="70cfe-181">Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="70cfe-181">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="70cfe-182">Zadania w kolejce w dół</span><span class="sxs-lookup"><span data-stu-id="70cfe-182">Queued background tasks</span></span>

<span data-ttu-id="70cfe-183">Kolejka zadań w tle jest oparta na platformie .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([wstępnie zaplanowano wbudowaną ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="70cfe-183">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="70cfe-184">W poniższym `QueueHostedService` przykładzie:</span><span class="sxs-lookup"><span data-stu-id="70cfe-184">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="70cfe-185">`BackgroundProcessing`Metoda zwraca obiekt `Task` , który jest oczekiwany w `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-185">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="70cfe-186">Zadania w tle w kolejce są dekolejkowane i wykonywane w `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-186">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="70cfe-187">Elementy robocze oczekują na zatrzymanie usługi w usłudze `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-187">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="70cfe-188">`MonitorLoop`Usługa obsługuje zadania umieszczenie dla usługi hostowanej za każdym razem, gdy `w` klucz zostanie wybrany na urządzeniu wejściowym:</span><span class="sxs-lookup"><span data-stu-id="70cfe-188">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="70cfe-189">`IBackgroundTaskQueue`Jest wstrzykiwana do `MonitorLoop` usługi.</span><span class="sxs-lookup"><span data-stu-id="70cfe-189">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="70cfe-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem`jest wywoływana w celu dodawania do kolejki elementu pracy.</span><span class="sxs-lookup"><span data-stu-id="70cfe-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="70cfe-191">Element roboczy symuluje długotrwałe zadanie w tle:</span><span class="sxs-lookup"><span data-stu-id="70cfe-191">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="70cfe-192">Trzy 5-sekundowe opóźnienia są wykonywane ( `Task.Delay` ).</span><span class="sxs-lookup"><span data-stu-id="70cfe-192">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="70cfe-193">`try-catch`Pułapka instrukcji <xref:System.OperationCanceledException> , jeśli zadanie zostało anulowane.</span><span class="sxs-lookup"><span data-stu-id="70cfe-193">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="70cfe-194">Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="70cfe-194">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="70cfe-195">Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="70cfe-195">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="70cfe-196">`MonitorLoop`jest uruchamiany w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="70cfe-196">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="70cfe-197">W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*.</span><span class="sxs-lookup"><span data-stu-id="70cfe-197">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="70cfe-198">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="70cfe-198">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="70cfe-199">Ten temat zawiera trzy przykłady usługi hostowanej:</span><span class="sxs-lookup"><span data-stu-id="70cfe-199">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="70cfe-200">Zadanie w tle, które jest uruchamiane na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="70cfe-200">Background task that runs on a timer.</span></span>
* <span data-ttu-id="70cfe-201">Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-201">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="70cfe-202">Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="70cfe-202">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="70cfe-203">Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-203">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="70cfe-204">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70cfe-204">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="70cfe-205">Pakiet</span><span class="sxs-lookup"><span data-stu-id="70cfe-205">Package</span></span>

<span data-ttu-id="70cfe-206">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-206">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="70cfe-207">IHostedService, interfejs</span><span class="sxs-lookup"><span data-stu-id="70cfe-207">IHostedService interface</span></span>

<span data-ttu-id="70cfe-208">Usługi hostowane implementują <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="70cfe-208">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="70cfe-209">Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:</span><span class="sxs-lookup"><span data-stu-id="70cfe-209">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="70cfe-210">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="70cfe-211">W przypadku korzystania z [hosta sieci Web](xref:fundamentals/host/web-host) `StartAsync` jest wywoływana po uruchomieniu serwera i wyzwoleniu [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-211">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="70cfe-212">W przypadku korzystania z [hosta generycznego](xref:fundamentals/host/generic-host) `StartAsync` jest wywoływana przed wywołaniem `ApplicationStarted` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-212">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="70cfe-213">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="70cfe-214">`StopAsync`zawiera logikę końcową zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-214">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="70cfe-215">Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="70cfe-215">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="70cfe-216">Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny.</span><span class="sxs-lookup"><span data-stu-id="70cfe-216">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="70cfe-217">Gdy żądanie anulowania jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="70cfe-217">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="70cfe-218">Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.</span><span class="sxs-lookup"><span data-stu-id="70cfe-218">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="70cfe-219">Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.</span><span class="sxs-lookup"><span data-stu-id="70cfe-219">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="70cfe-220">Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.</span><span class="sxs-lookup"><span data-stu-id="70cfe-220">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="70cfe-221">Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany.</span><span class="sxs-lookup"><span data-stu-id="70cfe-221">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="70cfe-222">W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="70cfe-222">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="70cfe-223">Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:</span><span class="sxs-lookup"><span data-stu-id="70cfe-223">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="70cfe-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>w przypadku korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="70cfe-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="70cfe-225">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="70cfe-225">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="70cfe-226">Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="70cfe-226">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="70cfe-227">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="70cfe-227">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="70cfe-228">Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="70cfe-228">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="70cfe-229">Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="70cfe-229">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="70cfe-230">Zadania w tle czasu</span><span class="sxs-lookup"><span data-stu-id="70cfe-230">Timed background tasks</span></span>

<span data-ttu-id="70cfe-231">Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="70cfe-231">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="70cfe-232">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="70cfe-232">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="70cfe-233">Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="70cfe-233">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="70cfe-234"><xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="70cfe-234">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="70cfe-235">Usługa jest zarejestrowana w ramach `Startup.ConfigureServices` `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="70cfe-235">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="70cfe-236">Zużywanie usługi w zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="70cfe-236">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="70cfe-237">Aby korzystać z usług o określonym [zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w ramach programu `IHostedService` , należy utworzyć zakres.</span><span class="sxs-lookup"><span data-stu-id="70cfe-237">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="70cfe-238">Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.</span><span class="sxs-lookup"><span data-stu-id="70cfe-238">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="70cfe-239">Usługa zadań w tle w zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="70cfe-239">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="70cfe-240">W poniższym przykładzie <xref:Microsoft.Extensions.Logging.ILogger> jest wstrzykiwana do usługi:</span><span class="sxs-lookup"><span data-stu-id="70cfe-240">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="70cfe-241">Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie w celu wywołania jej `DoWork` metody:</span><span class="sxs-lookup"><span data-stu-id="70cfe-241">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="70cfe-242">Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-242">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70cfe-243">`IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="70cfe-243">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="70cfe-244">Zadania w kolejce w dół</span><span class="sxs-lookup"><span data-stu-id="70cfe-244">Queued background tasks</span></span>

<span data-ttu-id="70cfe-245">Kolejka zadań w tle jest oparta na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([wstępnie zaplanowano wbudowaną ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="70cfe-245">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="70cfe-246">W programie `QueueHostedService` zadania w tle w kolejce są odrzucane i wykonywane jako [BackgroundService](#backgroundservice-base-class), która jest klasą bazową do implementowania długotrwałego działania `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="70cfe-246">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="70cfe-247">Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-247">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70cfe-248">`IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="70cfe-248">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="70cfe-249">W klasie modelu strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="70cfe-249">In the Index page model class:</span></span>

* <span data-ttu-id="70cfe-250">`IBackgroundTaskQueue`Zostaje wstrzyknięty do konstruktora i przypisany do `Queue` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-250">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="70cfe-251"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Jest wstrzykiwany i przypisany do `_serviceScopeFactory` .</span><span class="sxs-lookup"><span data-stu-id="70cfe-251">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="70cfe-252">Fabryka służy do tworzenia wystąpień <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> , które są używane do tworzenia usług w zakresie.</span><span class="sxs-lookup"><span data-stu-id="70cfe-252">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="70cfe-253">Zakres jest tworzony w celu używania aplikacji `AppDbContext` ( [usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes)) do zapisywania rekordów bazy danych w `IBackgroundTaskQueue` (pojedynczej usłudze).</span><span class="sxs-lookup"><span data-stu-id="70cfe-253">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="70cfe-254">Po wybraniu przycisku **Dodaj zadanie** na stronie indeks `OnPostAddTask` jest wykonywana metoda.</span><span class="sxs-lookup"><span data-stu-id="70cfe-254">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="70cfe-255">`QueueBackgroundWorkItem`jest wywoływana w celu dodawania do kolejki elementu pracy:</span><span class="sxs-lookup"><span data-stu-id="70cfe-255">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="70cfe-256">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="70cfe-256">Additional resources</span></span>

* [<span data-ttu-id="70cfe-257">Implementowanie zadań w tle w mikrousługach za pomocą IHostedService i klasy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="70cfe-257">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="70cfe-258">Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="70cfe-258">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
