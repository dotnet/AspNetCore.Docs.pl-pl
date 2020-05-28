---
<span data-ttu-id="f8675-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f8675-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8675-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8675-102">'Blazor'</span></span>
- <span data-ttu-id="f8675-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8675-103">'Identity'</span></span>
- <span data-ttu-id="f8675-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8675-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8675-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8675-105">'Razor'</span></span>
- <span data-ttu-id="f8675-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f8675-106">'SignalR' uid:</span></span> 

---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="f8675-107">Kontrole kondycji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8675-107">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="f8675-108">Autor [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="f8675-108">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8675-109">ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-109">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="f8675-110">Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP.</span><span class="sxs-lookup"><span data-stu-id="f8675-110">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="f8675-111">Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:</span><span class="sxs-lookup"><span data-stu-id="f8675-111">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="f8675-112">Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-112">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="f8675-113">Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera.</span><span class="sxs-lookup"><span data-stu-id="f8675-113">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="f8675-114">Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="f8675-114">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="f8675-115">Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.</span><span class="sxs-lookup"><span data-stu-id="f8675-115">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="f8675-116">Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.</span><span class="sxs-lookup"><span data-stu-id="f8675-116">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="f8675-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8675-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f8675-118">Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="f8675-118">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="f8675-119">Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="f8675-119">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="f8675-120">Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-120">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f8675-121">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f8675-121">Prerequisites</span></span>

<span data-ttu-id="f8675-122">Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-122">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="f8675-123">Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany.</span><span class="sxs-lookup"><span data-stu-id="f8675-123">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="f8675-124">System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="f8675-124">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="f8675-125">Pakiet [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) jest wywoływany niejawnie dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8675-125">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="f8675-126">Aby przeprowadzić kontrolę kondycji przy użyciu Entity Framework Core, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="f8675-126">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="f8675-127">Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="f8675-127">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="f8675-128">Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="f8675-128">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="f8675-129">Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f8675-129">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="f8675-130">Aby poznać scenariusze baz danych, przykładową aplikację:</span><span class="sxs-lookup"><span data-stu-id="f8675-130">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="f8675-131">Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f8675-131">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="f8675-132">W pliku projektu znajdują się następujące odwołania do pakietów:</span><span class="sxs-lookup"><span data-stu-id="f8675-132">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="f8675-133">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="f8675-133">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="f8675-134">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f8675-134">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="f8675-135">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-135">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="f8675-136">W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="f8675-136">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="f8675-137">Przykładowa aplikacja wymaga utworzenia pliku *Properties/profilu launchsettings. JSON* , który zawiera adres URL zarządzania i port zarządzania.</span><span class="sxs-lookup"><span data-stu-id="f8675-137">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="f8675-138">Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="f8675-138">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="f8675-139">Podstawowa sonda kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-139">Basic health probe</span></span>

<span data-ttu-id="f8675-140">W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania,*liveness*jest wystarczająca do odnajdywania stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-140">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="f8675-141">Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję.</span><span class="sxs-lookup"><span data-stu-id="f8675-141">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="f8675-142">Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu.</span><span class="sxs-lookup"><span data-stu-id="f8675-142">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="f8675-143">Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-143">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="f8675-144">Domyślny moduł zapisujący odpowiedzi zapisuje stan ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżone](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus stan złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="f8675-144">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="f8675-145">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-145">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-146">Utwórz punkt końcowy sprawdzania kondycji, wywołując `MapHealthChecks` w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-146">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="f8675-147">W przykładowej aplikacji jest tworzony punkt końcowy sprawdzania kondycji `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-147">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="f8675-148">Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-148">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="f8675-149">Przykład platformy Docker</span><span class="sxs-lookup"><span data-stu-id="f8675-149">Docker example</span></span>

<span data-ttu-id="f8675-150">Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-150">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="f8675-151">Utwórz kontrole kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-151">Create health checks</span></span>

<span data-ttu-id="f8675-152">Kontrole kondycji są tworzone przez implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="f8675-152">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="f8675-153"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda zwraca wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> wskazującą kondycję jako `Healthy` , `Degraded` lub `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="f8675-153">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="f8675-154">Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="f8675-154">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="f8675-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwracać opcjonalne pary klucz-wartość.</span><span class="sxs-lookup"><span data-stu-id="f8675-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="f8675-156">W poniższej `ExampleHealthCheck` klasie przedstawiono układ kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-156">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="f8675-157">Logika kontroli kondycji jest umieszczana w `CheckHealthAsync` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f8675-157">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="f8675-158">Poniższy przykład ustawia zmienną fikcyjną, `healthCheckResultHealthy` do `true` .</span><span class="sxs-lookup"><span data-stu-id="f8675-158">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="f8675-159">Jeśli wartość jest równa `healthCheckResultHealthy` `false` , zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="f8675-159">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="f8675-160">Rejestrowanie usług sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-160">Register health check services</span></span>

<span data-ttu-id="f8675-161">`ExampleHealthCheck`Typ jest dodawany do usług sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f8675-161">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="f8675-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Przeciążenie pokazane w poniższym przykładzie ustawia stan błędu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) w celu raportowania, kiedy Sprawdzanie kondycji zgłasza błąd.</span><span class="sxs-lookup"><span data-stu-id="f8675-162">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="f8675-163">Jeśli stan niepowodzenia jest ustawiony na wartość `null` (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="f8675-163">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="f8675-164">To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.</span><span class="sxs-lookup"><span data-stu-id="f8675-164">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="f8675-165">*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="f8675-165">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="f8675-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda.</span><span class="sxs-lookup"><span data-stu-id="f8675-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="f8675-167">W poniższym przykładzie nazwa sprawdzania kondycji jest określona jako, `Example` a sprawdzanie zawsze zwraca prawidłowy stan:</span><span class="sxs-lookup"><span data-stu-id="f8675-167">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="f8675-168">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> przekazywania argumentów do implementacji kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-168">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="f8675-169">W poniższym przykładzie `TestHealthCheckWithArgs` akceptuje liczbę całkowitą i ciąg do użycia, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="f8675-169">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="f8675-170">`TestHealthCheckWithArgs`jest zarejestrowany przez wywołanie `AddTypeActivatedCheck` z liczbą całkowitą i ciągiem przekazaną do implementacji:</span><span class="sxs-lookup"><span data-stu-id="f8675-170">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="f8675-171">Użyj routingu kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-171">Use Health Checks Routing</span></span>

<span data-ttu-id="f8675-172">W programie `Startup.Configure` Wywołaj program `MapHealthChecks` Endpoint Builder z adresem URL punktu końcowego lub ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="f8675-172">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="f8675-173">Wymagaj hosta</span><span class="sxs-lookup"><span data-stu-id="f8675-173">Require host</span></span>

<span data-ttu-id="f8675-174">Wywołaj, `RequireHost` Aby określić co najmniej jeden dozwolony Host dla punktu końcowego sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-174">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="f8675-175">Hosty powinny być w formacie Unicode, a nie formacie Punycode i mogą zawierać port.</span><span class="sxs-lookup"><span data-stu-id="f8675-175">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="f8675-176">Jeśli kolekcja nie zostanie podana, każdy host zostanie zaakceptowany.</span><span class="sxs-lookup"><span data-stu-id="f8675-176">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="f8675-177">Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="f8675-177">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="f8675-178">Wymagaj autoryzacji</span><span class="sxs-lookup"><span data-stu-id="f8675-178">Require authorization</span></span>

<span data-ttu-id="f8675-179">Wywołaj `RequireAuthorization` , aby uruchomić oprogramowanie pośredniczące autoryzacji w punkcie końcowym żądania sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-179">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="f8675-180">`RequireAuthorization`Przeciążenie akceptuje co najmniej jedną zasadę autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-180">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="f8675-181">Jeśli nie podano zasad, zostanie użyta domyślna zasada autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-181">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="f8675-182">Włączanie żądań Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="f8675-182">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="f8675-183">Mimo że kontrole kondycji są wykonywane ręcznie z przeglądarki, nie jest to typowy scenariusz użycia, można włączyć oprogramowanie do obsługi mechanizmu CORS, wywołując `RequireCors` Testy kondycji punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="f8675-183">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="f8675-184">`RequireCors`Przeciążenie akceptuje delegata konstruktora zasad CORS ( `CorsPolicyBuilder` ) lub nazwę zasady.</span><span class="sxs-lookup"><span data-stu-id="f8675-184">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="f8675-185">Jeśli nie podano zasad, zostanie użyta domyślna zasada CORS.</span><span class="sxs-lookup"><span data-stu-id="f8675-185">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="f8675-186">Aby uzyskać więcej informacji, zobacz <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="f8675-186">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="f8675-187">Opcje sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-187">Health check options</span></span>

<span data-ttu-id="f8675-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="f8675-189">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-189">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="f8675-190">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="f8675-190">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="f8675-191">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="f8675-191">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="f8675-192">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="f8675-192">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="f8675-193">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-193">Filter health checks</span></span>

<span data-ttu-id="f8675-194">Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-194">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="f8675-195">Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną dla <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji.</span><span class="sxs-lookup"><span data-stu-id="f8675-195">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="f8675-196">W poniższym przykładzie `Bar` Sprawdzanie kondycji jest odfiltrowane przez tag ( `bar_tag` ) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy właściwość sprawdzania kondycji jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> zgodna `foo_tag` lub `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="f8675-196">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="f8675-197">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f8675-197">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="f8675-198">W programie `Startup.Configure` program `Predicate` filtruje kontrolę kondycji "bar".</span><span class="sxs-lookup"><span data-stu-id="f8675-198">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="f8675-199">Tylko Foo i baz Execute.:</span><span class="sxs-lookup"><span data-stu-id="f8675-199">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="f8675-200">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="f8675-200">Customize the HTTP status code</span></span>

<span data-ttu-id="f8675-201">Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> , aby dostosować mapowanie stanu kondycji do kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f8675-201">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="f8675-202">Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="f8675-202">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="f8675-203">Zmień wartości kodów stanu, aby spełniały Twoje wymagania.</span><span class="sxs-lookup"><span data-stu-id="f8675-203">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="f8675-204">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8675-204">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="f8675-205">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="f8675-205">Suppress cache headers</span></span>

<span data-ttu-id="f8675-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Określa, czy kontrole kondycji powodują dodanie nagłówków HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f8675-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="f8675-207">Jeśli wartość jest `false` (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje `Cache-Control` `Expires` nagłówki, i, `Pragma` Aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f8675-207">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="f8675-208">Jeśli wartość to `true` , oprogramowanie pośredniczące nie modyfikuje nagłówków pamięci podręcznej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f8675-208">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="f8675-209">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8675-209">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="f8675-210">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="f8675-210">Customize output</span></span>

<span data-ttu-id="f8675-211">W programie `Startup.Configure` Ustaw dla opcji [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) delegata na potrzeby zapisywania odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f8675-211">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="f8675-212">Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="f8675-212">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="f8675-213">Następujące niestandardowe Delegaty wyprowadzają niestandardową odpowiedź JSON.</span><span class="sxs-lookup"><span data-stu-id="f8675-213">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="f8675-214">Pierwszy przykład z przykładowej aplikacji pokazuje, jak używać <xref:System.Text.Json?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="f8675-214">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="f8675-215">Drugi przykład ilustruje sposób użycia [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="f8675-215">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="f8675-216">W przykładowej aplikacji Dodaj komentarz do `SYSTEM_TEXT_JSON` [dyrektywy preprocesora](xref:index#preprocessor-directives-in-sample-code) w programie *CustomWriterStartup.cs* , aby włączyć `Newtonsoft.Json` wersję programu `WriteResponse` .</span><span class="sxs-lookup"><span data-stu-id="f8675-216">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="f8675-217">Interfejs API kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="f8675-217">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="f8675-218">W razie konieczności dostosuj odpowiedź w powyższych przykładach.</span><span class="sxs-lookup"><span data-stu-id="f8675-218">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="f8675-219">Aby uzyskać więcej informacji na temat serializacji JSON przy użyciu `System.Text.Json` , zobacz [jak serializować i DESERIALIZOWAĆ kod JSON w programie .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="f8675-219">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="f8675-220">Sonda bazy danych</span><span class="sxs-lookup"><span data-stu-id="f8675-220">Database probe</span></span>

<span data-ttu-id="f8675-221">Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="f8675-221">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="f8675-222">Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-222">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="f8675-223">`AspNetCore.Diagnostics.HealthChecks`wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-223">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="f8675-224">Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko.</span><span class="sxs-lookup"><span data-stu-id="f8675-224">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="f8675-225">Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności.</span><span class="sxs-lookup"><span data-stu-id="f8675-225">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="f8675-226">W większości przypadków uruchomienie zapytania testowego nie jest konieczne.</span><span class="sxs-lookup"><span data-stu-id="f8675-226">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="f8675-227">Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="f8675-227">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="f8675-228">Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="f8675-228">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="f8675-229">Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="f8675-229">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="f8675-230">Podaj prawidłowe parametry połączenia z bazą danych w pliku *appSettings. JSON* przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-230">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="f8675-231">Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="f8675-231">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="f8675-232">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-232">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-233">Przykładowa aplikacja wywołuje `AddSqlServer` metodę z parametrami połączenia bazy danych (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-233">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f8675-234">Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f8675-234">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="f8675-235">Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-235">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="f8675-236">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-236">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="f8675-237">Badanie Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="f8675-237">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="f8675-238">`DbContext`Sprawdzanie potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f8675-238">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="f8675-239">`DbContext`Sprawdzanie jest obsługiwane w aplikacjach, które:</span><span class="sxs-lookup"><span data-stu-id="f8675-239">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="f8675-240">Użyj [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="f8675-240">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="f8675-241">Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="f8675-241">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="f8675-242">`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla elementu `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f8675-242">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="f8675-243">`DbContext`Jest dostarczany jako `TContext` do metody.</span><span class="sxs-lookup"><span data-stu-id="f8675-243">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="f8675-244">Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.</span><span class="sxs-lookup"><span data-stu-id="f8675-244">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="f8675-245">Domyślnie:</span><span class="sxs-lookup"><span data-stu-id="f8675-245">By default:</span></span>

* <span data-ttu-id="f8675-246">`DbContextHealthCheck`Metoda wywołań EF Core `CanConnectAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8675-246">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="f8675-247">Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metod.</span><span class="sxs-lookup"><span data-stu-id="f8675-247">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="f8675-248">Nazwa sprawdzania kondycji jest nazwą `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="f8675-248">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="f8675-249">W przykładowej aplikacji `AppDbContext` jest dostarczany `AddDbContextCheck` i zarejestrowany jako usługa w `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-249">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f8675-250">Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f8675-250">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="f8675-251">Aby uruchomić `DbContext` scenariusz sondowania za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f8675-251">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="f8675-252">Jeśli baza danych istnieje, usuń ją.</span><span class="sxs-lookup"><span data-stu-id="f8675-252">If the database exists, delete it.</span></span>

<span data-ttu-id="f8675-253">Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-253">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="f8675-254">Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do `/health` punktu końcowego w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="f8675-254">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="f8675-255">Baza danych programu i `AppDbContext` nie istnieje, więc aplikacja udostępnia następujące odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f8675-255">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="f8675-256">Wyzwól przykładową aplikację, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-256">Trigger the sample app to create the database.</span></span> <span data-ttu-id="f8675-257">Wprowadź żądanie do `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="f8675-257">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="f8675-258">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="f8675-258">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="f8675-259">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="f8675-259">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="f8675-260">Istnieje baza danych i kontekst, aby aplikacja odpowiadała:</span><span class="sxs-lookup"><span data-stu-id="f8675-260">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="f8675-261">Wyzwól przykładową aplikację, aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-261">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="f8675-262">Wprowadź żądanie do `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="f8675-262">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="f8675-263">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="f8675-263">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="f8675-264">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="f8675-264">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="f8675-265">Aplikacja zawiera odpowiedź w złej kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-265">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="f8675-266">Oddzielne sondy gotowości i na żywo</span><span class="sxs-lookup"><span data-stu-id="f8675-266">Separate readiness and liveness probes</span></span>

<span data-ttu-id="f8675-267">W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f8675-267">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="f8675-268">Aplikacja działa, ale jeszcze nie jest gotowa do odbierania żądań.</span><span class="sxs-lookup"><span data-stu-id="f8675-268">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="f8675-269">Jest to stan *gotowości*aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-269">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="f8675-270">Aplikacja działa i odpowiada na żądania.</span><span class="sxs-lookup"><span data-stu-id="f8675-270">The app is functioning and responding to requests.</span></span> <span data-ttu-id="f8675-271">Ten stan jest *aktywny*.</span><span class="sxs-lookup"><span data-stu-id="f8675-271">This state is the app's *liveness*.</span></span>

<span data-ttu-id="f8675-272">Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli w celu ustalenia, czy wszystkie podsystemy i zasoby aplikacji są dostępne.</span><span class="sxs-lookup"><span data-stu-id="f8675-272">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="f8675-273">Sprawdzenie na żywo powoduje jedynie szybkie sprawdzenie, czy aplikacja jest dostępna do przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="f8675-273">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="f8675-274">Gdy aplikacja przejdzie kontrolę gotowości, nie ma potrzeby dalszej obciążania aplikacji przy użyciu kosztownego zestawu kontroli gotowości &mdash; sprawdza tylko, czy sprawdzanie dostępności jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="f8675-274">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="f8675-275">Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="f8675-275">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="f8675-276">`StartupHostedServiceHealthCheck`Uwidacznia właściwość, `StartupTaskCompleted` która może zostać ustawiona przez usługę hostowaną `true` po zakończeniu długotrwałego zadania (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-276">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="f8675-277">Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) (*usługi/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="f8675-277">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="f8675-278">Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony na `true` :</span><span class="sxs-lookup"><span data-stu-id="f8675-278">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="f8675-279">Kontrola kondycji jest zarejestrowana <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w programie w programie `Startup.ConfigureServices` wraz z usługą hostowaną.</span><span class="sxs-lookup"><span data-stu-id="f8675-279">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="f8675-280">Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-280">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f8675-281">Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-281">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="f8675-282">W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone w:</span><span class="sxs-lookup"><span data-stu-id="f8675-282">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="f8675-283">`/health/ready`w celu sprawdzenia gotowości.</span><span class="sxs-lookup"><span data-stu-id="f8675-283">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="f8675-284">Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji za pomocą `ready` znacznika.</span><span class="sxs-lookup"><span data-stu-id="f8675-284">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="f8675-285">`/health/live`na potrzeby kontroli na żywo.</span><span class="sxs-lookup"><span data-stu-id="f8675-285">`/health/live` for the liveness check.</span></span> <span data-ttu-id="f8675-286">Sprawdzenie stanu na żywo odfiltruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` elementu [HealthCheckOptions. predykatu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, [zobacz Sprawdzanie kondycji filtru](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="f8675-286">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="f8675-287">W poniższym przykładowym kodzie:</span><span class="sxs-lookup"><span data-stu-id="f8675-287">In the following example code:</span></span>

* <span data-ttu-id="f8675-288">Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".</span><span class="sxs-lookup"><span data-stu-id="f8675-288">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="f8675-289">`Predicate`Wyklucza wszystkie testy i zwracają 200-OK.</span><span class="sxs-lookup"><span data-stu-id="f8675-289">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="f8675-290">Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-290">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="f8675-291">W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-291">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="f8675-292">Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-292">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="f8675-293">Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.</span><span class="sxs-lookup"><span data-stu-id="f8675-293">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="f8675-294">W tym przykładzie tworzony jest również Wydawca sprawdzania kondycji ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacja), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="f8675-294">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="f8675-295">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="f8675-295">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="f8675-296">Przykład Kubernetes</span><span class="sxs-lookup"><span data-stu-id="f8675-296">Kubernetes example</span></span>

<span data-ttu-id="f8675-297">Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="f8675-297">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="f8675-298">W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-298">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="f8675-299">Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="f8675-299">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="f8675-300">Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="f8675-300">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="f8675-301">Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="f8675-301">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="f8675-302">Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="f8675-302">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="f8675-303">Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="f8675-303">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="f8675-304">`MemoryHealthCheck`zgłasza stan obniżonej wydajności, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji).</span><span class="sxs-lookup"><span data-stu-id="f8675-304">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="f8675-305"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zawiera informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-305">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="f8675-306">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-306">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-307">Zamiast włączać kontrolę kondycji przez przekazanie jej do programu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jest zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="f8675-307">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="f8675-308">Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="f8675-308">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="f8675-309">Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="f8675-309">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="f8675-310">W *CustomWriterStartup.cs* przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f8675-310">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="f8675-311">Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-311">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="f8675-312">`WriteResponse`Delegat jest dostarczany do właściwości <Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions. ResponseWriter> do wyprowadzania niestandardowej odpowiedzi JSON, gdy jest wykonywane sprawdzanie kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-312">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="f8675-313">`WriteResponse`Delegat formatuje `CompositeHealthCheckResult` obiekt w formacie JSON i zwraca dane wyjściowe JSON dla odpowiedzi kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-313">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="f8675-314">Aby uzyskać więcej informacji, zobacz sekcję [Dostosowywanie danych wyjściowych](#customize-output) .</span><span class="sxs-lookup"><span data-stu-id="f8675-314">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="f8675-315">Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-315">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="f8675-316">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.</span><span class="sxs-lookup"><span data-stu-id="f8675-316">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="f8675-317">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-317">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="f8675-318">Filtruj według portu</span><span class="sxs-lookup"><span data-stu-id="f8675-318">Filter by port</span></span>

<span data-ttu-id="f8675-319">Zadzwoń do `RequireHost` `MapHealthChecks` wzorca adresu URL, który określa port, aby ograniczyć żądania sprawdzania kondycji do określonego portu.</span><span class="sxs-lookup"><span data-stu-id="f8675-319">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="f8675-320">Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.</span><span class="sxs-lookup"><span data-stu-id="f8675-320">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="f8675-321">Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f8675-321">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f8675-322">Port jest ustawiany w pliku *profilu launchsettings. JSON* i przekazywać do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="f8675-322">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="f8675-323">Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.</span><span class="sxs-lookup"><span data-stu-id="f8675-323">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="f8675-324">Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz plik *profilu launchsettings. JSON* w folderze *Właściwości* .</span><span class="sxs-lookup"><span data-stu-id="f8675-324">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="f8675-325">Następujące *właściwości/profilu launchsettings. JSON* w przykładowej aplikacji nie znajdują się w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:</span><span class="sxs-lookup"><span data-stu-id="f8675-325">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="f8675-326">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-326">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-327">Utwórz punkt końcowy sprawdzania kondycji, wywołując `MapHealthChecks` w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-327">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="f8675-328">W przykładowej aplikacji wywołanie do `RequireHost` punktu końcowego w programie `Startup.Configure` określa port zarządzania z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="f8675-328">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="f8675-329">Punkty końcowe są tworzone w aplikacji przykładowej w programie `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-329">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="f8675-330">W poniższym przykładowym kodzie:</span><span class="sxs-lookup"><span data-stu-id="f8675-330">In the following example code:</span></span>

* <span data-ttu-id="f8675-331">Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".</span><span class="sxs-lookup"><span data-stu-id="f8675-331">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="f8675-332">`Predicate`Wyklucza wszystkie testy i zwracają 200-OK.</span><span class="sxs-lookup"><span data-stu-id="f8675-332">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="f8675-333">Można uniknąć tworzenia pliku *profilu launchsettings. JSON* w przykładowej aplikacji przez ustawienie portu zarządzania jawnie w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f8675-333">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="f8675-334">W *program.cs* , gdzie <xref:Microsoft.Extensions.Hosting.HostBuilder> został utworzony, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> i Podaj punkt końcowy portu zarządzania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-334">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="f8675-335">W `Configure` programie *ManagementPortStartup.cs*określ port zarządzania przy użyciu `RequireHost` :</span><span class="sxs-lookup"><span data-stu-id="f8675-335">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="f8675-336">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8675-336">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="f8675-337">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8675-337">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="f8675-338">Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-338">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="f8675-339">Dystrybucja biblioteki kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-339">Distribute a health check library</span></span>

<span data-ttu-id="f8675-340">Aby rozpowszechnić kontrolę kondycji jako bibliotekę:</span><span class="sxs-lookup"><span data-stu-id="f8675-340">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="f8675-341">Napisz kontrolę kondycji, która implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejs jako autonomiczną klasę.</span><span class="sxs-lookup"><span data-stu-id="f8675-341">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="f8675-342">Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="f8675-342">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="f8675-343">W logice kontroli kondycji `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="f8675-343">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="f8675-344">`data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.</span><span class="sxs-lookup"><span data-stu-id="f8675-344">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="f8675-345">`AccessViolationException`jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-345">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="f8675-346">Gdy wystąpi <xref:System.AccessViolationException> , <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostaje zwrócony z, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-346">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="f8675-347">Napisz metodę rozszerzenia z parametrami, które są używane przez zużywaną aplikację w jej `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f8675-347">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="f8675-348">W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-348">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="f8675-349">Poprzednia sygnatura wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-349">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="f8675-350">Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="f8675-350">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="f8675-351">W poniższym przykładzie obiekt wywołujący określa opcjonalne:</span><span class="sxs-lookup"><span data-stu-id="f8675-351">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="f8675-352">Nazwa sprawdzania kondycji ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-352">health check name (`name`).</span></span> <span data-ttu-id="f8675-353">Jeśli `null` `example_health_check` jest używany.</span><span class="sxs-lookup"><span data-stu-id="f8675-353">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="f8675-354">punkt danych ciągu dla kontroli kondycji ( `data1` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-354">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="f8675-355">punkt danych liczb całkowitych dla kontroli kondycji ( `data2` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-355">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="f8675-356">Jeśli `null` `1` jest używany.</span><span class="sxs-lookup"><span data-stu-id="f8675-356">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="f8675-357">stan niepowodzenia ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="f8675-357">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="f8675-358">Wartość domyślna to `null`.</span><span class="sxs-lookup"><span data-stu-id="f8675-358">The default is `null`.</span></span> <span data-ttu-id="f8675-359">Jeśli `null` dla stanu błędu zostanie zgłoszona wartość [HealthStatus. w złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="f8675-359">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="f8675-360">Tagi ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-360">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="f8675-361">Wydawca kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-361">Health Check Publisher</span></span>

<span data-ttu-id="f8675-362">Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system kontroli kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` wynik.</span><span class="sxs-lookup"><span data-stu-id="f8675-362">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="f8675-363">Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-363">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="f8675-364"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Interfejs ma jedną metodę:</span><span class="sxs-lookup"><span data-stu-id="f8675-364">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="f8675-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>Zezwól na ustawienie:</span><span class="sxs-lookup"><span data-stu-id="f8675-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="f8675-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Początkowe opóźnienie stosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="f8675-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="f8675-367">Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji.</span><span class="sxs-lookup"><span data-stu-id="f8675-367">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="f8675-368">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-368">The default value is five seconds.</span></span>
* <span data-ttu-id="f8675-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wykonania.</span><span class="sxs-lookup"><span data-stu-id="f8675-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="f8675-370">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-370">The default value is 30 seconds.</span></span>
* <span data-ttu-id="f8675-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="f8675-372">Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli.</span><span class="sxs-lookup"><span data-stu-id="f8675-372">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="f8675-373">Predykat jest oceniany w każdym okresie.</span><span class="sxs-lookup"><span data-stu-id="f8675-373">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="f8675-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Limit czasu wykonywania kontroli kondycji dla wszystkich <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="f8675-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="f8675-375">Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan> , aby wykonać bez limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="f8675-375">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="f8675-376">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-376">The default value is 30 seconds.</span></span>

<span data-ttu-id="f8675-377">W przykładowej aplikacji `ReadinessPublisher` jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacją.</span><span class="sxs-lookup"><span data-stu-id="f8675-377">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="f8675-378">Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia na poziomie dziennika:</span><span class="sxs-lookup"><span data-stu-id="f8675-378">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="f8675-379">Informacja ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), jeśli stan kontroli kondycji to <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="f8675-379">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="f8675-380">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="f8675-380">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="f8675-381">W `LivenessProbeStartup` przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa drugie opóźnienie uruchamiania i sprawdza, co 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-381">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="f8675-382">Aby uaktywnić <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, przykład rejestruje się `ReadinessPublisher` jako usługa pojedyncza w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="f8675-382">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="f8675-383">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="f8675-383">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="f8675-384">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-384">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="f8675-385">Ogranicz kontrolę kondycji za pomocą MapWhen</span><span class="sxs-lookup"><span data-stu-id="f8675-385">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="f8675-386">Użyj, <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> Aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-386">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="f8675-387">W poniższym przykładzie `MapWhen` rozgałęzienie potoku żądania w celu aktywowania kontroli kondycji oprogramowania pośredniczącego w przypadku otrzymania żądania GET dla `api/HealthCheck` punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="f8675-387">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="f8675-388">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="f8675-388">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8675-389">ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-389">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="f8675-390">Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP.</span><span class="sxs-lookup"><span data-stu-id="f8675-390">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="f8675-391">Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:</span><span class="sxs-lookup"><span data-stu-id="f8675-391">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="f8675-392">Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-392">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="f8675-393">Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera.</span><span class="sxs-lookup"><span data-stu-id="f8675-393">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="f8675-394">Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="f8675-394">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="f8675-395">Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.</span><span class="sxs-lookup"><span data-stu-id="f8675-395">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="f8675-396">Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.</span><span class="sxs-lookup"><span data-stu-id="f8675-396">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="f8675-397">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8675-397">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f8675-398">Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="f8675-398">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="f8675-399">Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="f8675-399">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="f8675-400">Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-400">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f8675-401">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f8675-401">Prerequisites</span></span>

<span data-ttu-id="f8675-402">Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-402">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="f8675-403">Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany.</span><span class="sxs-lookup"><span data-stu-id="f8675-403">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="f8675-404">System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="f8675-404">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="f8675-405">Odwołującego się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu w pakiecie [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .</span><span class="sxs-lookup"><span data-stu-id="f8675-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="f8675-406">Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="f8675-406">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="f8675-407">Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="f8675-407">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="f8675-408">Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f8675-408">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="f8675-409">Aby poznać scenariusze baz danych, przykładową aplikację:</span><span class="sxs-lookup"><span data-stu-id="f8675-409">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="f8675-410">Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f8675-410">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="f8675-411">W pliku projektu znajdują się następujące odwołania do pakietów:</span><span class="sxs-lookup"><span data-stu-id="f8675-411">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="f8675-412">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="f8675-412">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="f8675-413">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f8675-413">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="f8675-414">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-414">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="f8675-415">W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="f8675-415">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="f8675-416">Przykładowa aplikacja wymaga utworzenia pliku *Properties/profilu launchsettings. JSON* , który zawiera adres URL zarządzania i port zarządzania.</span><span class="sxs-lookup"><span data-stu-id="f8675-416">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="f8675-417">Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="f8675-417">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="f8675-418">Podstawowa sonda kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-418">Basic health probe</span></span>

<span data-ttu-id="f8675-419">W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania,*liveness*jest wystarczająca do odnajdywania stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-419">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="f8675-420">Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję.</span><span class="sxs-lookup"><span data-stu-id="f8675-420">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="f8675-421">Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu.</span><span class="sxs-lookup"><span data-stu-id="f8675-421">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="f8675-422">Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-422">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="f8675-423">Domyślny moduł zapisujący odpowiedzi zapisuje stan ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżone](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus stan złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="f8675-423">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="f8675-424">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-424">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-425">Dodaj punkt końcowy dla programu testowego kondycji programu z <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> programu w potoku przetwarzania żądania `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-425">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="f8675-426">W przykładowej aplikacji jest tworzony punkt końcowy sprawdzania kondycji `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-426">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="f8675-427">Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-427">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="f8675-428">Przykład platformy Docker</span><span class="sxs-lookup"><span data-stu-id="f8675-428">Docker example</span></span>

<span data-ttu-id="f8675-429">Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-429">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="f8675-430">Utwórz kontrole kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-430">Create health checks</span></span>

<span data-ttu-id="f8675-431">Kontrole kondycji są tworzone przez implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="f8675-431">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="f8675-432"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda zwraca wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> wskazującą kondycję jako `Healthy` , `Degraded` lub `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="f8675-432">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="f8675-433">Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="f8675-433">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="f8675-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwracać opcjonalne pary klucz-wartość.</span><span class="sxs-lookup"><span data-stu-id="f8675-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="f8675-435">Przykładowe Sprawdzenie kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-435">Example health check</span></span>

<span data-ttu-id="f8675-436">W poniższej `ExampleHealthCheck` klasie przedstawiono układ kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-436">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="f8675-437">Logika kontroli kondycji jest umieszczana w `CheckHealthAsync` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f8675-437">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="f8675-438">Poniższy przykład ustawia zmienną fikcyjną, `healthCheckResultHealthy` do `true` .</span><span class="sxs-lookup"><span data-stu-id="f8675-438">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="f8675-439">Jeśli wartość jest równa `healthCheckResultHealthy` `false` , zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="f8675-439">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="f8675-440">Rejestrowanie usług sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-440">Register health check services</span></span>

<span data-ttu-id="f8675-441">`ExampleHealthCheck`Typ jest dodawany do usług sprawdzania kondycji w programie w `Startup.ConfigureServices` programie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :</span><span class="sxs-lookup"><span data-stu-id="f8675-441">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="f8675-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Przeciążenie pokazane w poniższym przykładzie ustawia stan błędu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) w celu raportowania, kiedy Sprawdzanie kondycji zgłasza błąd.</span><span class="sxs-lookup"><span data-stu-id="f8675-442">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="f8675-443">Jeśli stan niepowodzenia jest ustawiony na wartość `null` (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="f8675-443">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="f8675-444">To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.</span><span class="sxs-lookup"><span data-stu-id="f8675-444">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="f8675-445">*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="f8675-445">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="f8675-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda.</span><span class="sxs-lookup"><span data-stu-id="f8675-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="f8675-447">W poniższym `Startup.ConfigureServices` przykładzie nazwa sprawdzania kondycji jest określona jako, `Example` a sprawdzanie zawsze zwraca prawidłowy stan:</span><span class="sxs-lookup"><span data-stu-id="f8675-447">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="f8675-448">Używanie oprogramowania do sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-448">Use Health Checks Middleware</span></span>

<span data-ttu-id="f8675-449">W programie `Startup.Configure` Wywołaj <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> w potoku przetwarzania z adresem URL punktu końcowego lub ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="f8675-449">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="f8675-450">Jeśli kontrole kondycji powinny nasłuchiwać określonego portu, Użyj przeciążenia, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Aby ustawić port (dokładniej opisane w sekcji [Filtruj według portu](#filter-by-port) ):</span><span class="sxs-lookup"><span data-stu-id="f8675-450">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="f8675-451">Opcje sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-451">Health check options</span></span>

<span data-ttu-id="f8675-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="f8675-453">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-453">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="f8675-454">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="f8675-454">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="f8675-455">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="f8675-455">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="f8675-456">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="f8675-456">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="f8675-457">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-457">Filter health checks</span></span>

<span data-ttu-id="f8675-458">Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-458">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="f8675-459">Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną dla <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji.</span><span class="sxs-lookup"><span data-stu-id="f8675-459">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="f8675-460">W poniższym przykładzie `Bar` Sprawdzanie kondycji jest odfiltrowane przez tag ( `bar_tag` ) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy właściwość sprawdzania kondycji jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> zgodna `foo_tag` lub `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="f8675-460">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="f8675-461">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="f8675-461">Customize the HTTP status code</span></span>

<span data-ttu-id="f8675-462">Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> , aby dostosować mapowanie stanu kondycji do kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f8675-462">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="f8675-463">Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="f8675-463">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="f8675-464">Zmień wartości kodów stanu, aby spełniały Twoje wymagania.</span><span class="sxs-lookup"><span data-stu-id="f8675-464">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="f8675-465">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8675-465">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="f8675-466">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="f8675-466">Suppress cache headers</span></span>

<span data-ttu-id="f8675-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Określa, czy kontrole kondycji powodują dodanie nagłówków HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f8675-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="f8675-468">Jeśli wartość jest `false` (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje `Cache-Control` `Expires` nagłówki, i, `Pragma` Aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f8675-468">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="f8675-469">Jeśli wartość to `true` , oprogramowanie pośredniczące nie modyfikuje nagłówków pamięci podręcznej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f8675-469">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="f8675-470">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8675-470">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="f8675-471">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="f8675-471">Customize output</span></span>

<span data-ttu-id="f8675-472"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter>Opcja Pobiera lub ustawia delegata używany do zapisywania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f8675-472">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="f8675-473">Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="f8675-473">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="f8675-474">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8675-474">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="f8675-475">Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="f8675-475">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="f8675-476">Następujący delegat niestandardowy, `WriteResponse` , wyprowadza niestandardową odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="f8675-476">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="f8675-477">System kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="f8675-477">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="f8675-478">Możesz dowolnie dostosowywać `JObject` w powyższym przykładzie, aby zaspokoić Twoje potrzeby.</span><span class="sxs-lookup"><span data-stu-id="f8675-478">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="f8675-479">Sonda bazy danych</span><span class="sxs-lookup"><span data-stu-id="f8675-479">Database probe</span></span>

<span data-ttu-id="f8675-480">Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="f8675-480">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="f8675-481">Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-481">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="f8675-482">`AspNetCore.Diagnostics.HealthChecks`wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-482">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="f8675-483">Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko.</span><span class="sxs-lookup"><span data-stu-id="f8675-483">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="f8675-484">Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności.</span><span class="sxs-lookup"><span data-stu-id="f8675-484">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="f8675-485">W większości przypadków uruchomienie zapytania testowego nie jest konieczne.</span><span class="sxs-lookup"><span data-stu-id="f8675-485">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="f8675-486">Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="f8675-486">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="f8675-487">Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="f8675-487">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="f8675-488">Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="f8675-488">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="f8675-489">Podaj prawidłowe parametry połączenia z bazą danych w pliku *appSettings. JSON* przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-489">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="f8675-490">Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="f8675-490">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="f8675-491">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-491">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-492">Przykładowa aplikacja wywołuje `AddSqlServer` metodę z parametrami połączenia bazy danych (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-492">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f8675-493">Wywołaj kontrolę kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f8675-493">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="f8675-494">Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-494">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="f8675-495">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-495">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="f8675-496">Badanie Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="f8675-496">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="f8675-497">`DbContext`Sprawdzanie potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f8675-497">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="f8675-498">`DbContext`Sprawdzanie jest obsługiwane w aplikacjach, które:</span><span class="sxs-lookup"><span data-stu-id="f8675-498">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="f8675-499">Użyj [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="f8675-499">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="f8675-500">Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="f8675-500">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="f8675-501">`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla elementu `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f8675-501">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="f8675-502">`DbContext`Jest dostarczany jako `TContext` do metody.</span><span class="sxs-lookup"><span data-stu-id="f8675-502">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="f8675-503">Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.</span><span class="sxs-lookup"><span data-stu-id="f8675-503">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="f8675-504">Domyślnie:</span><span class="sxs-lookup"><span data-stu-id="f8675-504">By default:</span></span>

* <span data-ttu-id="f8675-505">`DbContextHealthCheck`Metoda wywołań EF Core `CanConnectAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8675-505">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="f8675-506">Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metod.</span><span class="sxs-lookup"><span data-stu-id="f8675-506">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="f8675-507">Nazwa sprawdzania kondycji jest nazwą `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="f8675-507">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="f8675-508">W przykładowej aplikacji `AppDbContext` jest dostarczany `AddDbContextCheck` i zarejestrowany jako usługa w `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-508">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f8675-509">W przykładowej aplikacji program `UseHealthChecks` dodaje do programu oprogramowanie do sprawdzania kondycji `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-509">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="f8675-510">Aby uruchomić `DbContext` scenariusz sondowania za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f8675-510">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="f8675-511">Jeśli baza danych istnieje, usuń ją.</span><span class="sxs-lookup"><span data-stu-id="f8675-511">If the database exists, delete it.</span></span>

<span data-ttu-id="f8675-512">Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-512">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="f8675-513">Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do `/health` punktu końcowego w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="f8675-513">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="f8675-514">Baza danych programu i `AppDbContext` nie istnieje, więc aplikacja udostępnia następujące odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="f8675-514">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="f8675-515">Wyzwól przykładową aplikację, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-515">Trigger the sample app to create the database.</span></span> <span data-ttu-id="f8675-516">Wprowadź żądanie do `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="f8675-516">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="f8675-517">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="f8675-517">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="f8675-518">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="f8675-518">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="f8675-519">Istnieje baza danych i kontekst, aby aplikacja odpowiadała:</span><span class="sxs-lookup"><span data-stu-id="f8675-519">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="f8675-520">Wyzwól przykładową aplikację, aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-520">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="f8675-521">Wprowadź żądanie do `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="f8675-521">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="f8675-522">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="f8675-522">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="f8675-523">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="f8675-523">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="f8675-524">Aplikacja zawiera odpowiedź w złej kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-524">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="f8675-525">Oddzielne sondy gotowości i na żywo</span><span class="sxs-lookup"><span data-stu-id="f8675-525">Separate readiness and liveness probes</span></span>

<span data-ttu-id="f8675-526">W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f8675-526">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="f8675-527">Aplikacja działa, ale jeszcze nie jest gotowa do odbierania żądań.</span><span class="sxs-lookup"><span data-stu-id="f8675-527">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="f8675-528">Jest to stan *gotowości*aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-528">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="f8675-529">Aplikacja działa i odpowiada na żądania.</span><span class="sxs-lookup"><span data-stu-id="f8675-529">The app is functioning and responding to requests.</span></span> <span data-ttu-id="f8675-530">Ten stan jest *aktywny*.</span><span class="sxs-lookup"><span data-stu-id="f8675-530">This state is the app's *liveness*.</span></span>

<span data-ttu-id="f8675-531">Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli w celu ustalenia, czy wszystkie podsystemy i zasoby aplikacji są dostępne.</span><span class="sxs-lookup"><span data-stu-id="f8675-531">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="f8675-532">Sprawdzenie na żywo powoduje jedynie szybkie sprawdzenie, czy aplikacja jest dostępna do przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="f8675-532">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="f8675-533">Gdy aplikacja przejdzie kontrolę gotowości, nie ma potrzeby dalszej obciążania aplikacji przy użyciu kosztownego zestawu kontroli gotowości &mdash; sprawdza tylko, czy sprawdzanie dostępności jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="f8675-533">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="f8675-534">Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="f8675-534">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="f8675-535">`StartupHostedServiceHealthCheck`Uwidacznia właściwość, `StartupTaskCompleted` która może zostać ustawiona przez usługę hostowaną `true` po zakończeniu długotrwałego zadania (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-535">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="f8675-536">Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) (*usługi/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="f8675-536">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="f8675-537">Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony na `true` :</span><span class="sxs-lookup"><span data-stu-id="f8675-537">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="f8675-538">Kontrola kondycji jest zarejestrowana <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w programie w programie `Startup.ConfigureServices` wraz z usługą hostowaną.</span><span class="sxs-lookup"><span data-stu-id="f8675-538">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="f8675-539">Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-539">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f8675-540">Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w programie `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-540">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="f8675-541">W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone na `/health/ready` potrzeby kontroli gotowości i `/health/live` kontroli stanu na żywo.</span><span class="sxs-lookup"><span data-stu-id="f8675-541">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="f8675-542">Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji za pomocą `ready` znacznika.</span><span class="sxs-lookup"><span data-stu-id="f8675-542">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="f8675-543">Sprawdzenie stanu na żywo odfiltruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` elementu [HealthCheckOptions. predykatu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, [zobacz Sprawdzanie kondycji filtru](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="f8675-543">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="f8675-544">Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-544">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="f8675-545">W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-545">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="f8675-546">Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-546">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="f8675-547">Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.</span><span class="sxs-lookup"><span data-stu-id="f8675-547">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="f8675-548">W tym przykładzie tworzony jest również Wydawca sprawdzania kondycji ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacja), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="f8675-548">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="f8675-549">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="f8675-549">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="f8675-550">Przykład Kubernetes</span><span class="sxs-lookup"><span data-stu-id="f8675-550">Kubernetes example</span></span>

<span data-ttu-id="f8675-551">Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="f8675-551">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="f8675-552">W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f8675-552">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="f8675-553">Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="f8675-553">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="f8675-554">Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="f8675-554">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="f8675-555">Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="f8675-555">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="f8675-556">Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="f8675-556">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="f8675-557">Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="f8675-557">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="f8675-558">`MemoryHealthCheck`zgłasza stan złej kondycji, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji).</span><span class="sxs-lookup"><span data-stu-id="f8675-558">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="f8675-559"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zawiera informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-559">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="f8675-560">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-560">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-561">Zamiast włączać kontrolę kondycji przez przekazanie jej do programu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jest zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="f8675-561">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="f8675-562">Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="f8675-562">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="f8675-563">Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="f8675-563">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="f8675-564">W przykładowej aplikacji (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-564">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="f8675-565">Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w programie `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f8675-565">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="f8675-566">`WriteResponse`Obiekt delegowany jest dostarczany do `ResponseWriter` właściwości w celu wygenerowania NIESTANDARDOWEJ odpowiedzi JSON po zakończeniu sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-566">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="f8675-567">`WriteResponse`Metoda formatuje `CompositeHealthCheckResult` do obiektu JSON i zwraca dane wyjściowe JSON dla odpowiedzi kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-567">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="f8675-568">Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-568">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="f8675-569">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.</span><span class="sxs-lookup"><span data-stu-id="f8675-569">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="f8675-570">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-570">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="f8675-571">Filtruj według portu</span><span class="sxs-lookup"><span data-stu-id="f8675-571">Filter by port</span></span>

<span data-ttu-id="f8675-572">Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> z portem ogranicza liczbę żądań sprawdzania kondycji do określonego portu.</span><span class="sxs-lookup"><span data-stu-id="f8675-572">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="f8675-573">Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.</span><span class="sxs-lookup"><span data-stu-id="f8675-573">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="f8675-574">Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f8675-574">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f8675-575">Port jest ustawiany w pliku *profilu launchsettings. JSON* i przekazywać do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="f8675-575">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="f8675-576">Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.</span><span class="sxs-lookup"><span data-stu-id="f8675-576">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="f8675-577">Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz plik *profilu launchsettings. JSON* w folderze *Właściwości* .</span><span class="sxs-lookup"><span data-stu-id="f8675-577">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="f8675-578">Następujące *właściwości/profilu launchsettings. JSON* w przykładowej aplikacji nie znajdują się w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:</span><span class="sxs-lookup"><span data-stu-id="f8675-578">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="f8675-579">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f8675-579">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8675-580">Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> określające port zarządzania (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8675-580">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="f8675-581">Możesz uniknąć tworzenia pliku *profilu launchsettings. JSON* w przykładowej aplikacji, ustawiając adresy URL i port zarządzania jawnie w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f8675-581">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="f8675-582">W *program.cs* , w którym <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> został utworzony, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> i podaj normalny punkt końcowy odpowiedzi aplikacji oraz punkt końcowy portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="f8675-582">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="f8675-583">W *ManagementPortStartup.cs* <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> , gdzie jest wywoływana, określ port zarządzania jawnie.</span><span class="sxs-lookup"><span data-stu-id="f8675-583">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="f8675-584">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8675-584">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="f8675-585">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8675-585">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="f8675-586">Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8675-586">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="f8675-587">Dystrybucja biblioteki kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-587">Distribute a health check library</span></span>

<span data-ttu-id="f8675-588">Aby rozpowszechnić kontrolę kondycji jako bibliotekę:</span><span class="sxs-lookup"><span data-stu-id="f8675-588">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="f8675-589">Napisz kontrolę kondycji, która implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejs jako autonomiczną klasę.</span><span class="sxs-lookup"><span data-stu-id="f8675-589">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="f8675-590">Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="f8675-590">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="f8675-591">W logice kontroli kondycji `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="f8675-591">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="f8675-592">`data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.</span><span class="sxs-lookup"><span data-stu-id="f8675-592">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="f8675-593">`AccessViolationException`jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-593">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="f8675-594">Gdy wystąpi <xref:System.AccessViolationException> , <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostaje zwrócony z, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-594">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="f8675-595">Napisz metodę rozszerzenia z parametrami, które są używane przez zużywaną aplikację w jej `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="f8675-595">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="f8675-596">W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="f8675-596">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="f8675-597">Poprzednia sygnatura wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-597">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="f8675-598">Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="f8675-598">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="f8675-599">W poniższym przykładzie obiekt wywołujący określa opcjonalne:</span><span class="sxs-lookup"><span data-stu-id="f8675-599">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="f8675-600">Nazwa sprawdzania kondycji ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-600">health check name (`name`).</span></span> <span data-ttu-id="f8675-601">Jeśli `null` `example_health_check` jest używany.</span><span class="sxs-lookup"><span data-stu-id="f8675-601">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="f8675-602">punkt danych ciągu dla kontroli kondycji ( `data1` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-602">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="f8675-603">punkt danych liczb całkowitych dla kontroli kondycji ( `data2` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-603">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="f8675-604">Jeśli `null` `1` jest używany.</span><span class="sxs-lookup"><span data-stu-id="f8675-604">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="f8675-605">stan niepowodzenia ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="f8675-605">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="f8675-606">Wartość domyślna to `null`.</span><span class="sxs-lookup"><span data-stu-id="f8675-606">The default is `null`.</span></span> <span data-ttu-id="f8675-607">Jeśli `null` dla stanu błędu zostanie zgłoszona wartość [HealthStatus. w złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="f8675-607">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="f8675-608">Tagi ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="f8675-608">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="f8675-609">Wydawca kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="f8675-609">Health Check Publisher</span></span>

<span data-ttu-id="f8675-610">Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system kontroli kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` wynik.</span><span class="sxs-lookup"><span data-stu-id="f8675-610">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="f8675-611">Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-611">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="f8675-612"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Interfejs ma jedną metodę:</span><span class="sxs-lookup"><span data-stu-id="f8675-612">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="f8675-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>Zezwól na ustawienie:</span><span class="sxs-lookup"><span data-stu-id="f8675-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="f8675-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Początkowe opóźnienie stosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="f8675-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="f8675-615">Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji.</span><span class="sxs-lookup"><span data-stu-id="f8675-615">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="f8675-616">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-616">The default value is five seconds.</span></span>
* <span data-ttu-id="f8675-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wykonania.</span><span class="sxs-lookup"><span data-stu-id="f8675-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="f8675-618">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-618">The default value is 30 seconds.</span></span>
* <span data-ttu-id="f8675-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="f8675-620">Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli.</span><span class="sxs-lookup"><span data-stu-id="f8675-620">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="f8675-621">Predykat jest oceniany w każdym okresie.</span><span class="sxs-lookup"><span data-stu-id="f8675-621">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="f8675-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Limit czasu wykonywania kontroli kondycji dla wszystkich <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="f8675-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="f8675-623">Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan> , aby wykonać bez limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="f8675-623">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="f8675-624">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-624">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="f8675-625">W wersji 2,2 ASP.NET Core ustawienie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nie jest honorowane przez <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację; ustawia wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> .</span><span class="sxs-lookup"><span data-stu-id="f8675-625">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="f8675-626">Ten problem został rozwiązany w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f8675-626">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="f8675-627">W przykładowej aplikacji `ReadinessPublisher` jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacją.</span><span class="sxs-lookup"><span data-stu-id="f8675-627">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="f8675-628">Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia jako:</span><span class="sxs-lookup"><span data-stu-id="f8675-628">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="f8675-629">Informacja ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), jeśli stan kontroli kondycji to <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="f8675-629">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="f8675-630">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="f8675-630">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="f8675-631">W `LivenessProbeStartup` przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa drugie opóźnienie uruchamiania i sprawdza, co 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f8675-631">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="f8675-632">Aby uaktywnić <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, przykład rejestruje się `ReadinessPublisher` jako usługa pojedyncza w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="f8675-632">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="f8675-633">Następujące obejście umożliwia dodanie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpienia do kontenera usługi, gdy co najmniej jedna inna usługa hostowana została już dodana do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8675-633">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="f8675-634">To obejście nie będzie wymagane w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f8675-634">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="f8675-635">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="f8675-635">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="f8675-636">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8675-636">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="f8675-637">Ogranicz kontrolę kondycji za pomocą MapWhen</span><span class="sxs-lookup"><span data-stu-id="f8675-637">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="f8675-638">Użyj, <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> Aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="f8675-638">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="f8675-639">W poniższym przykładzie `MapWhen` rozgałęzienie potoku żądania w celu aktywowania kontroli kondycji oprogramowania pośredniczącego w przypadku otrzymania żądania GET dla `api/HealthCheck` punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="f8675-639">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="f8675-640">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="f8675-640">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
