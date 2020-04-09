---
title: Kontrole zdrowia w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować kontrole kondycji ASP.NET podstawowej infrastruktury, takiej jak aplikacje i bazy danych.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 314e55c818cddf1dad2e3ec74d4d1e041ce7366f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664886"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="cc27b-103">Kontrole zdrowia w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc27b-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="cc27b-104">Przez [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="cc27b-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc27b-105">ASP.NET Core oferuje oprogramowanie pośredniczące i biblioteki health checks do raportowania kondycji składników infrastruktury aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="cc27b-106">Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc27b-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="cc27b-107">Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:</span><span class="sxs-lookup"><span data-stu-id="cc27b-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="cc27b-108">Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="cc27b-109">Na przykład orchestrator kontenera może odpowiedzieć na sprawdzanie kondycji wada, zatrzymując wdrożenie stopniowe lub ponowne uruchomienie kontenera.</span><span class="sxs-lookup"><span data-stu-id="cc27b-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="cc27b-110">Moduł równoważenia obciążenia może reagować na złej aplikacji przez routing ruchu z dala od wystąpienia awarii do wystąpienia w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="cc27b-111">Korzystanie z pamięci, dysku i innych zasobów serwera fizycznego można monitorować pod kątem stanu zdrowia.</span><span class="sxs-lookup"><span data-stu-id="cc27b-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="cc27b-112">Kontrole kondycji można przetestować zależności aplikacji, takich jak bazy danych i punktów końcowych usługi zewnętrznej, aby potwierdzić dostępność i normalne funkcjonowanie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="cc27b-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cc27b-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cc27b-114">Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="cc27b-115">Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="cc27b-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="cc27b-116">Zobacz przykładowy plik *README.md* aplikacji i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc27b-117">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cc27b-117">Prerequisites</span></span>

<span data-ttu-id="cc27b-118">Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenerów w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="cc27b-119">Przed dodaniem kontroli kondycji do aplikacji, zdecydować, który system monitorowania do użycia.</span><span class="sxs-lookup"><span data-stu-id="cc27b-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="cc27b-120">System monitorowania określa, jakie typy kontroli kondycji do utworzenia i jak skonfigurować ich punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="cc27b-121">Pakiet [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) odwołuje się niejawnie do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc27b-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="cc27b-122">Aby przeprowadzić kontrole kondycji przy użyciu programu Entity Framework Core, dodaj odwołanie do pakietu [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore.](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="cc27b-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="cc27b-123">Przykładowa aplikacja udostępnia kod startowy, aby zademonstrować testy kondycji dla kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="cc27b-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="cc27b-124">Scenariusz [sondy bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="cc27b-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="cc27b-125">Scenariusz [sondy DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę `DbContext`danych przy użyciu ef core .</span><span class="sxs-lookup"><span data-stu-id="cc27b-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="cc27b-126">Aby eksplorować scenariusze bazy danych, przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="cc27b-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="cc27b-127">Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="cc27b-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="cc27b-128">Ma następujące odwołania do pakietu w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="cc27b-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="cc27b-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="cc27b-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="cc27b-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="cc27b-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="cc27b-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="cc27b-132">Inny scenariusz sprawdzania kondycji pokazuje, jak filtrować kontrole kondycji do portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="cc27b-133">Przykładowa aplikacja wymaga utworzenia pliku *Properties/launchSettings.json,* który zawiera adres URL zarządzania i port zarządzania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="cc27b-134">Aby uzyskać więcej informacji, zobacz [sekcję Filtruj według portów.](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="cc27b-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="cc27b-135">Podstawowa sonda kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-135">Basic health probe</span></span>

<span data-ttu-id="cc27b-136">W przypadku wielu aplikacji podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do przetwarzania żądań *(żywotność)* jest wystarczająca do odnajdowania stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="cc27b-137">Podstawowa konfiguracja rejestruje usługi sprawdzania kondycji i wywołuje oprogramowanie pośredniczące kontroli kondycji, aby odpowiedzieć w punkcie końcowym adresu URL z odpowiedzią kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="cc27b-138">Domyślnie nie są rejestrowane żadne określone kontrole kondycji w celu przetestowania określonej zależności lub podsystemu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="cc27b-139">Aplikacja jest uważana za w dobrej kondycji, jeśli jest w stanie odpowiedzieć na adres URL punktu końcowego kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="cc27b-140">Domyślny moduł zapisujący odpowiedzi<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapisuje stan ( ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, wskazując stan [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)</span><span class="sxs-lookup"><span data-stu-id="cc27b-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="cc27b-141">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-142">Utwórz punkt końcowy sprawdzania `MapHealthChecks` `Startup.Configure`kondycji, wywołując program .</span><span class="sxs-lookup"><span data-stu-id="cc27b-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="cc27b-143">W przykładowej aplikacji punkt końcowy oceny `/health` kondycji jest tworzony w (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc27b-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="cc27b-144">Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="cc27b-145">Przykład platformy Docker</span><span class="sxs-lookup"><span data-stu-id="cc27b-145">Docker example</span></span>

<span data-ttu-id="cc27b-146">[Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, która może służyć do sprawdzania stanu aplikacji, która używa podstawowej konfiguracji sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="cc27b-147">Tworzenie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-147">Create health checks</span></span>

<span data-ttu-id="cc27b-148">Kontrole kondycji są tworzone <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> przez implementowanie interfejsu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="cc27b-149">Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zwraca, który wskazuje kondycję `Degraded`jako `Unhealthy` `Healthy`, lub .</span><span class="sxs-lookup"><span data-stu-id="cc27b-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="cc27b-150">Wynik jest zapisywany jako odpowiedź w postaci zwykłego tekstu z konfigurowalnym kodem stanu (konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji).](#health-check-options)</span><span class="sxs-lookup"><span data-stu-id="cc27b-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="cc27b-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwrócić opcjonalne pary klucz-wartość.</span><span class="sxs-lookup"><span data-stu-id="cc27b-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="cc27b-152">W `ExampleHealthCheck` poniższej klasie przedstawiono układ kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="cc27b-153">Logika kontroli kondycji jest `CheckHealthAsync` umieszczana w metodzie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="cc27b-154">W poniższym przykładzie ustawia `healthCheckResultHealthy`zmienną manekina, na . `true`</span><span class="sxs-lookup"><span data-stu-id="cc27b-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="cc27b-155">Jeśli wartość `healthCheckResultHealthy` jest ustawiona na `false`, [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stan jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="cc27b-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="cc27b-156">Zarejestruj usługi sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-156">Register health check services</span></span>

<span data-ttu-id="cc27b-157">Typ `ExampleHealthCheck` jest dodawany do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> usług `Startup.ConfigureServices`kontroli kondycji z w:</span><span class="sxs-lookup"><span data-stu-id="cc27b-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="cc27b-158">Przeciążenie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> pokazane w poniższym przykładzie<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>ustawia stan błędu ( ) do raportu, gdy sprawdzanie kondycji zgłasza błąd.</span><span class="sxs-lookup"><span data-stu-id="cc27b-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="cc27b-159">Jeśli stan błędu jest `null` ustawiony na (domyślnie), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane.</span><span class="sxs-lookup"><span data-stu-id="cc27b-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="cc27b-160">To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan awarii wskazany przez bibliotekę jest wymuszany przez aplikację, gdy wystąpi błąd sprawdzania kondycji, jeśli implementacja sprawdzania kondycji honoruje to ustawienie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="cc27b-161">*Tagi* mogą służyć do filtrowania kontroli kondycji (opisane dalej w [sekcji Filtrowanie kontroli kondycji).](#filter-health-checks)</span><span class="sxs-lookup"><span data-stu-id="cc27b-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="cc27b-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda.</span><span class="sxs-lookup"><span data-stu-id="cc27b-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="cc27b-163">W poniższym przykładzie nazwa sprawdzania `Example` kondycji jest określona jako i sprawdzanie zawsze zwraca stan dobrej kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="cc27b-164">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> przekazywania argumentów do implementacji sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="cc27b-165">W poniższym `TestHealthCheckWithArgs` przykładzie akceptuje całkowitą i ciąg do <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> użycia, gdy jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="cc27b-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="cc27b-166">`TestHealthCheckWithArgs`jest rejestrowany `AddTypeActivatedCheck` przez wywołanie z całkowitej liczby i ciąg przekazany do implementacji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="cc27b-167">Korzystanie z routingu kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-167">Use Health Checks Routing</span></span>

<span data-ttu-id="cc27b-168">W `Startup.Configure`programie `MapHealthChecks` wywołaj konstruktora punktów końcowych z adresem URL punktu końcowego lub ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="cc27b-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="cc27b-169">Wymagaj hosta</span><span class="sxs-lookup"><span data-stu-id="cc27b-169">Require host</span></span>

<span data-ttu-id="cc27b-170">Wywołanie, `RequireHost` aby określić jeden lub więcej dozwolonych hostów dla punktu końcowego sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="cc27b-171">Hosty powinny być Unicode, a nie punycode i może zawierać port.</span><span class="sxs-lookup"><span data-stu-id="cc27b-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="cc27b-172">Jeśli kolekcja nie jest dostarczana, każdy host jest akceptowany.</span><span class="sxs-lookup"><span data-stu-id="cc27b-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="cc27b-173">Aby uzyskać więcej informacji, zobacz [sekcję Filtruj według portów.](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="cc27b-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="cc27b-174">Wymagaj autoryzacji</span><span class="sxs-lookup"><span data-stu-id="cc27b-174">Require authorization</span></span>

<span data-ttu-id="cc27b-175">Wywołanie `RequireAuthorization` uruchomienia oprogramowania pośredniczącego autoryzacji w punkcie końcowym żądania sprawdzenia kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="cc27b-176">Przeciążenie `RequireAuthorization` akceptuje co najmniej jedną zasadę autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="cc27b-177">Jeśli zasady nie są dostarczane, używana jest domyślna zasada autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="cc27b-178">Włączanie żądań Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="cc27b-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="cc27b-179">Chociaż ręczne wykonywanie kontroli kondycji z przeglądarki nie jest typowym scenariuszem użycia, oprogramowanie pośredniczące CORS można włączyć, wywołując `RequireCors` punkty końcowe kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="cc27b-180">Przeciążenie `RequireCors` akceptuje delegata konstruktora`CorsPolicyBuilder`zasad CORS ( ) lub nazwę zasad.</span><span class="sxs-lookup"><span data-stu-id="cc27b-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="cc27b-181">Jeśli zasady nie są podane, używana jest domyślna zasada CORS.</span><span class="sxs-lookup"><span data-stu-id="cc27b-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="cc27b-182">Aby uzyskać więcej informacji, zobacz <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="cc27b-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="cc27b-183">Opcje sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-183">Health check options</span></span>

<span data-ttu-id="cc27b-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="cc27b-185">Sprawdzanie kondycji filtrowania</span><span class="sxs-lookup"><span data-stu-id="cc27b-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="cc27b-186">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="cc27b-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="cc27b-187">Pomijanie nagłówków pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="cc27b-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="cc27b-188">Dostosowywanie danych wyjściowych</span><span class="sxs-lookup"><span data-stu-id="cc27b-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="cc27b-189">Sprawdzanie kondycji filtrowania</span><span class="sxs-lookup"><span data-stu-id="cc27b-189">Filter health checks</span></span>

<span data-ttu-id="cc27b-190">Domyślnie oprogramowanie pośredniczące health checks uruchamia wszystkie zarejestrowane kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="cc27b-191">Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która zwraca wartość logiczną <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> do opcji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="cc27b-192">W poniższym przykładzie `Bar` sprawdzanie kondycji jest odfiltrowywało według jego znacznika (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracane tylko wtedy, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> właściwość sprawdzania kondycji jest zgodna `foo_tag` lub: `baz_tag`</span><span class="sxs-lookup"><span data-stu-id="cc27b-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="cc27b-193">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="cc27b-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="cc27b-194">W `Startup.Configure`, `Predicate` odfiltruje "Bar" kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="cc27b-195">Tylko Foo i Baz wykonać.:</span><span class="sxs-lookup"><span data-stu-id="cc27b-195">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="cc27b-196">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="cc27b-196">Customize the HTTP status code</span></span>

<span data-ttu-id="cc27b-197">Służy <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> do dostosowywania mapowania stanu kondycji do kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc27b-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="cc27b-198">Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="cc27b-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="cc27b-199">Zmień wartości kodu stanu, aby spełnić wymagania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="cc27b-200">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cc27b-200">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="cc27b-201">Pomijanie nagłówków pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="cc27b-201">Suppress cache headers</span></span>

<span data-ttu-id="cc27b-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>określa, czy oprogramowanie pośredniczące kontroli kondycji dodaje nagłówki HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cc27b-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="cc27b-203">Jeśli wartość `false` jest (domyślna), oprogramowanie pośredniczące `Cache-Control` `Expires`ustawia `Pragma` lub zastępuje , i nagłówki, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cc27b-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="cc27b-204">Jeśli wartość `true`jest , oprogramowanie pośredniczące nie modyfikuje nagłówki pamięci podręcznej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cc27b-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="cc27b-205">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cc27b-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="cc27b-206">Dostosowywanie danych wyjściowych</span><span class="sxs-lookup"><span data-stu-id="cc27b-206">Customize output</span></span>

<span data-ttu-id="cc27b-207">W `Startup.Configure`, ustaw [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) opcji delegata do pisania odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="cc27b-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="cc27b-208">Domyślny delegat zapisuje minimalną odpowiedź w postaci zwykłego tekstu z wartością ciągu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="cc27b-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="cc27b-209">Następujące delegatów niestandardowych danych wyjściowych niestandardowej odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="cc27b-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="cc27b-210">Pierwszy przykład z przykładowej aplikacji pokazuje, jak używać: <xref:System.Text.Json?displayProperty=fullName></span><span class="sxs-lookup"><span data-stu-id="cc27b-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="cc27b-211">Drugi przykład pokazuje, jak korzystać z [Newtonsoft.Json:](https://www.nuget.org/packages/Newtonsoft.Json/)</span><span class="sxs-lookup"><span data-stu-id="cc27b-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="cc27b-212">W przykładowej aplikacji skomentuj `SYSTEM_TEXT_JSON` [dyrektywę preprocesora](xref:index#preprocessor-directives-in-sample-code) w `Newtonsoft.Json` *CustomWriterStartup.cs,* aby włączyć wersję programu `WriteResponse`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="cc27b-213">Interfejs API kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów zwracanych JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="cc27b-214">Dostosuj odpowiedź w poprzednich przykładach w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="cc27b-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="cc27b-215">Aby uzyskać więcej informacji na `System.Text.Json`temat serializacji JSON za pomocą , zobacz [Jak serializować i deserialize JSON w .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="cc27b-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="cc27b-216">Sonda bazy danych</span><span class="sxs-lookup"><span data-stu-id="cc27b-216">Database probe</span></span>

<span data-ttu-id="cc27b-217">Sprawdzanie kondycji można określić kwerendę bazy danych do uruchomienia jako test logiczny, aby wskazać, czy baza danych odpowiada normalnie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="cc27b-218">Przykładowa aplikacja używa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, do sprawdzania kondycji w bazie danych programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cc27b-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="cc27b-219">`AspNetCore.Diagnostics.HealthChecks`wykonuje kwerendę do `SELECT 1` bazy danych, aby potwierdzić, że połączenie z bazą danych jest w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="cc27b-220">Podczas sprawdzania połączenia bazy danych z kwerendą wybierz kwerendę, która zwraca szybko.</span><span class="sxs-lookup"><span data-stu-id="cc27b-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="cc27b-221">Podejście zapytania ryzykuje przeciążenie bazy danych i obniżenie jej wydajności.</span><span class="sxs-lookup"><span data-stu-id="cc27b-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="cc27b-222">W większości przypadków uruchamianie kwerendy testowej nie jest konieczne.</span><span class="sxs-lookup"><span data-stu-id="cc27b-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="cc27b-223">Wystarczy jedynie pomyślne połączenie z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="cc27b-224">Jeśli okaże się konieczne uruchomienie kwerendy, wybierz proste `SELECT 1`zapytanie SELECT, takie jak .</span><span class="sxs-lookup"><span data-stu-id="cc27b-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="cc27b-225">Dołącz odwołanie do pakietu [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="cc27b-226">Podaj prawidłowy ciąg połączenia bazy danych w pliku *appsettings.json* przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="cc27b-227">Aplikacja korzysta z bazy danych `HealthCheckSample`programu SQL Server o nazwie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="cc27b-228">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-229">Przykładowa aplikacja `AddSqlServer` wywołuje metodę z ciągiem połączenia bazy danych *(DbHealthStartup.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="cc27b-230">Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w:</span><span class="sxs-lookup"><span data-stu-id="cc27b-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="cc27b-231">Aby uruchomić scenariusz sondy bazy danych przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="cc27b-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="cc27b-233">Sonda Core DbContext programu Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cc27b-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="cc27b-234">Sprawdzanie `DbContext` potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla ef core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="cc27b-235">Sprawdzanie `DbContext` jest obsługiwane w aplikacjach, które:</span><span class="sxs-lookup"><span data-stu-id="cc27b-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="cc27b-236">Użyj [core (Entity Framework) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="cc27b-237">Dołącz odwołanie do pakietu [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="cc27b-238">`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="cc27b-239">Jest `DbContext` dostarczany jako `TContext` do metody.</span><span class="sxs-lookup"><span data-stu-id="cc27b-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="cc27b-240">Przeciążenie jest dostępne do skonfigurowania stanu błędu, tagów i niestandardowej kwerendy testowej.</span><span class="sxs-lookup"><span data-stu-id="cc27b-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="cc27b-241">Domyślnie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-241">By default:</span></span>

* <span data-ttu-id="cc27b-242">Wywołanie `DbContextHealthCheck` metody EF `CanConnectAsync` Core.</span><span class="sxs-lookup"><span data-stu-id="cc27b-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="cc27b-243">Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metody.</span><span class="sxs-lookup"><span data-stu-id="cc27b-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="cc27b-244">Nazwa sprawdzania kondycji jest nazwą `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="cc27b-245">W przykładowej `AppDbContext` aplikacji, `AddDbContextCheck` jest dostarczany i `Startup.ConfigureServices` zarejestrowany jako usługa w (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc27b-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="cc27b-246">Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w:</span><span class="sxs-lookup"><span data-stu-id="cc27b-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="cc27b-247">Aby uruchomić `DbContext` scenariusz sondowania przy użyciu przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cc27b-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="cc27b-248">Jeśli baza danych istnieje, usuń ją.</span><span class="sxs-lookup"><span data-stu-id="cc27b-248">If the database exists, delete it.</span></span>

<span data-ttu-id="cc27b-249">Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="cc27b-250">Po uruchomieniu aplikacji sprawdź stan kondycji, wysyłając żądanie `/health` do punktu końcowego w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="cc27b-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="cc27b-251">Baza danych `AppDbContext` i nie istnieje, więc aplikacja zapewnia następującą odpowiedź:</span><span class="sxs-lookup"><span data-stu-id="cc27b-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="cc27b-252">Wyzwalanie przykładowej aplikacji, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="cc27b-253">Złożyć wniosek `/createdatabase`do .</span><span class="sxs-lookup"><span data-stu-id="cc27b-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="cc27b-254">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="cc27b-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="cc27b-255">Złożyć żądanie `/health` do punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="cc27b-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="cc27b-256">Istnieje baza danych i kontekst, więc aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="cc27b-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="cc27b-257">Wyzwalanie przykładowej aplikacji, aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="cc27b-258">Złożyć wniosek `/deletedatabase`do .</span><span class="sxs-lookup"><span data-stu-id="cc27b-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="cc27b-259">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="cc27b-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="cc27b-260">Złożyć żądanie `/health` do punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="cc27b-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="cc27b-261">Aplikacja zapewnia w złej kondycji odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="cc27b-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="cc27b-262">Oddzielne sondy gotowości i żywotności</span><span class="sxs-lookup"><span data-stu-id="cc27b-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="cc27b-263">W niektórych scenariuszach hostingu używana jest para kontroli kondycji, które rozróżniają dwa stany aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="cc27b-264">Aplikacja działa, ale nie jest jeszcze gotowa do odbierania żądań.</span><span class="sxs-lookup"><span data-stu-id="cc27b-264">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="cc27b-265">Ten stan jest *gotowość*aplikacji .</span><span class="sxs-lookup"><span data-stu-id="cc27b-265">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="cc27b-266">Aplikacja działa i odpowiada na żądania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-266">The app is functioning and responding to requests.</span></span> <span data-ttu-id="cc27b-267">Ten stan jest *żywość*aplikacji .</span><span class="sxs-lookup"><span data-stu-id="cc27b-267">This state is the app's *liveness*.</span></span>

<span data-ttu-id="cc27b-268">Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli, aby ustalić, czy wszystkie podsystemy i zasoby aplikacji są dostępne.</span><span class="sxs-lookup"><span data-stu-id="cc27b-268">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="cc27b-269">Sprawdzanie żywotności wykonuje tylko szybkie sprawdzenie, aby ustalić, czy aplikacja jest dostępna do przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="cc27b-269">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="cc27b-270">Po przejściu przez aplikację kontroli gotowości, nie ma potrzeby, aby obciążyć&mdash;aplikację dalej z drogim zestawem kontroli gotowości dalsze kontrole wymagają tylko sprawdzenia, czy nie ma żywotności.</span><span class="sxs-lookup"><span data-stu-id="cc27b-270">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="cc27b-271">Przykładowa aplikacja zawiera sprawdzanie kondycji, aby zgłosić zakończenie długotrwałego zadania uruchamiania w [usłudze hosted](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="cc27b-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="cc27b-272">Udostępnia `StartupHostedServiceHealthCheck` właściwość, `StartupTaskCompleted`że usługa hostowana można `true` ustawić po zakończeniu jej długotrwałe zadanie *(StartupHostedServiceHealthCheck.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="cc27b-273">Długotrwałe zadanie w tle jest uruchamiane przez [usługę hostowana](xref:fundamentals/host/hosted-services) *(Usługi/StartupHostedService).*</span><span class="sxs-lookup"><span data-stu-id="cc27b-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="cc27b-274">Po zakończeniu zadania, `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony `true`na:</span><span class="sxs-lookup"><span data-stu-id="cc27b-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="cc27b-275">Kontrola kondycji jest <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zarejestrowana w `Startup.ConfigureServices` wraz z usługą hosta.</span><span class="sxs-lookup"><span data-stu-id="cc27b-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="cc27b-276">Ponieważ usługa hostowana musi ustawić właściwość na kontroli kondycji, kontrola kondycji jest również zarejestrowana w kontenerze serwisowym (*LivenessProbeStartup.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="cc27b-277">Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w .</span><span class="sxs-lookup"><span data-stu-id="cc27b-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="cc27b-278">W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone w:</span><span class="sxs-lookup"><span data-stu-id="cc27b-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="cc27b-279">`/health/ready`do kontroli gotowości.</span><span class="sxs-lookup"><span data-stu-id="cc27b-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="cc27b-280">Sprawdzanie gotowości filtruje kontrole kondycji do sprawdzenia `ready` kondycji za pomocą tagu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="cc27b-281">`/health/live`do sprawdzania żywotności.</span><span class="sxs-lookup"><span data-stu-id="cc27b-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="cc27b-282">Sprawdzanie żywotności filtruje `StartupHostedServiceHealthCheck` przez `false` powrót w [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (aby uzyskać więcej informacji, zobacz [filtrowanie kontroli kondycji)](#filter-health-checks)</span><span class="sxs-lookup"><span data-stu-id="cc27b-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="cc27b-283">W poniższym przykładzie kodu:</span><span class="sxs-lookup"><span data-stu-id="cc27b-283">In the following example code:</span></span>

* <span data-ttu-id="cc27b-284">Kontrola gotowości używa wszystkich zarejestrowanych czeków z tagiem "ready".</span><span class="sxs-lookup"><span data-stu-id="cc27b-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="cc27b-285">Wyklucza `Predicate` wszystkie kontrole i zwraca 200-Ok.</span><span class="sxs-lookup"><span data-stu-id="cc27b-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="cc27b-286">Aby uruchomić scenariusz konfiguracji gotowości/żywotności przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="cc27b-287">W przeglądarce `/health/ready` odwiedź kilka razy, aż minęło 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="cc27b-288">Kontrola kondycji raportuje *wzdrowy sposób* przez pierwsze 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="cc27b-289">Po 15 sekundach punkt końcowy raportuje *w dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania przez hostowany serwis.</span><span class="sxs-lookup"><span data-stu-id="cc27b-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="cc27b-290">W tym przykładzie tworzy<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> również Health Check Publisher (implementacja), który uruchamia pierwsze sprawdzanie gotowości z dwusekundowym opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="cc27b-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="cc27b-291">Aby uzyskać więcej informacji, zobacz sekcję [Wydawca sprawdzania kondycji.](#health-check-publisher)</span><span class="sxs-lookup"><span data-stu-id="cc27b-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="cc27b-292">Przykład Kubernetes</span><span class="sxs-lookup"><span data-stu-id="cc27b-292">Kubernetes example</span></span>

<span data-ttu-id="cc27b-293">Korzystanie z oddzielnych kontroli gotowości i żywotności jest przydatne w środowisku takim jak [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="cc27b-294">W umięsierze aplikacja może być wymagana do wykonywania czasochłonnych prac podczas uruchamiania przed zaakceptowaniem żądań, takich jak test podstawowej dostępności bazy danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="cc27b-295">Za pomocą oddzielnych kontroli umożliwia orchestrator odróżnić, czy aplikacja działa, ale nie jest jeszcze gotowy lub jeśli aplikacja nie powiodło się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="cc27b-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="cc27b-296">Aby uzyskać więcej informacji na temat gotowości i sondy żywotności w umięsienia, zobacz [Konfigurowanie sondy żywotności i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji programu Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="cc27b-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="cc27b-297">W poniższym przykładzie pokazano konfigurację sondy gotowości Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="cc27b-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="cc27b-298">Sonda oparta na metrykach z modułem zapisu odpowiedzi niestandardowej</span><span class="sxs-lookup"><span data-stu-id="cc27b-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="cc27b-299">Przykładowa aplikacja pokazuje sprawdzanie kondycji pamięci za pomocą modułu zapisującego odpowiedzi niestandardowej.</span><span class="sxs-lookup"><span data-stu-id="cc27b-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="cc27b-300">`MemoryHealthCheck`zgłasza stan obniżony, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji).</span><span class="sxs-lookup"><span data-stu-id="cc27b-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="cc27b-301">Informacje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> o odpadach odśmiecania (GC) dla aplikacji *(MemoryHealthCheck.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="cc27b-302">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-303">Zamiast włączać sprawdzanie kondycji, przekazując <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>go `MemoryHealthCheck` do , jest zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="cc27b-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="cc27b-304">Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług kontroli kondycji i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="cc27b-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="cc27b-305">Zalecamy rejestrowanie usług kontroli kondycji jako usług Singleton.</span><span class="sxs-lookup"><span data-stu-id="cc27b-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="cc27b-306">W *CustomWriterStartup.cs* przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="cc27b-307">Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w .</span><span class="sxs-lookup"><span data-stu-id="cc27b-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="cc27b-308">Pełnomocnik `WriteResponse` jest dostarczany do <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> właściwości do wysuczenia niestandardowej odpowiedzi JSON podczas wykonywania sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="cc27b-309">Delegat `WriteResponse` formatuje `CompositeHealthCheckResult` do obiektu JSON i daje wyjście JSON dla odpowiedzi na sprawdzenie kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="cc27b-310">Aby uzyskać więcej informacji, zobacz sekcję [Dostosowywanie danych wyjściowych.](#customize-output)</span><span class="sxs-lookup"><span data-stu-id="cc27b-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="cc27b-311">Aby uruchomić sondę opartą na metrykach z niestandardowym wyjściem modułu zapisującego odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="cc27b-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zawiera scenariusze sprawdzania kondycji oparte na metrykach, w tym sprawdzanie żywotności dysku i maksymalnej wartości.</span><span class="sxs-lookup"><span data-stu-id="cc27b-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="cc27b-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="cc27b-314">Filtrowanie według portów</span><span class="sxs-lookup"><span data-stu-id="cc27b-314">Filter by port</span></span>

<span data-ttu-id="cc27b-315">`RequireHost` Wywołanie `MapHealthChecks` z wzorcem adresu URL, który określa port, aby ograniczyć żądania sprawdzania kondycji do określonego portu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="cc27b-316">Jest to zwykle używane w środowisku kontenera, aby udostępnić port dla usług monitorowania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="cc27b-317">Przykładowa aplikacja konfiguruje port przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cc27b-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="cc27b-318">Port jest ustawiony w pliku *launchSettings.json* i przekazywany do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="cc27b-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="cc27b-319">Należy również skonfigurować serwer do nasłuchiwać żądań na porcie zarządzania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="cc27b-320">Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portu zarządzania, utwórz plik *launchSettings.json* w folderze *Właściwości.*</span><span class="sxs-lookup"><span data-stu-id="cc27b-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="cc27b-321">Następujący plik *Properties/launchSettings.json* w przykładowej aplikacji nie jest uwzględniony w przykładowych plikach projektu aplikacji i musi zostać utworzony ręcznie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="cc27b-322">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-323">Utwórz punkt końcowy sprawdzania `MapHealthChecks` `Startup.Configure`kondycji, wywołując program .</span><span class="sxs-lookup"><span data-stu-id="cc27b-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="cc27b-324">W przykładowej aplikacji wywołanie `RequireHost` punktu końcowego w `Startup.Configure` określa port zarządzania z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="cc27b-325">Punkty końcowe są tworzone w `Startup.Configure`przykładowej aplikacji w programie .</span><span class="sxs-lookup"><span data-stu-id="cc27b-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="cc27b-326">W poniższym przykładzie kodu:</span><span class="sxs-lookup"><span data-stu-id="cc27b-326">In the following example code:</span></span>

* <span data-ttu-id="cc27b-327">Kontrola gotowości używa wszystkich zarejestrowanych czeków z tagiem "ready".</span><span class="sxs-lookup"><span data-stu-id="cc27b-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="cc27b-328">Wyklucza `Predicate` wszystkie kontrole i zwraca 200-Ok.</span><span class="sxs-lookup"><span data-stu-id="cc27b-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="cc27b-329">Można uniknąć tworzenia *pliku launchSettings.json* w przykładowej aplikacji, ustawiając port zarządzania jawnie w kodzie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="cc27b-330">W *Program.cs,* w <xref:Microsoft.Extensions.Hosting.HostBuilder> którym jest tworzony, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> dodaj wywołanie i podaj punkt końcowy portu zarządzania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="cc27b-331">W `Configure` *ManagementPortStartup.cs*określić port zarządzania `RequireHost`za pomocą:</span><span class="sxs-lookup"><span data-stu-id="cc27b-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="cc27b-332">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="cc27b-332">*Program.cs*:</span></span>
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
> <span data-ttu-id="cc27b-333">*ManagementPortStartup.cs:*</span><span class="sxs-lookup"><span data-stu-id="cc27b-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="cc27b-334">Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="cc27b-335">Rozpowszechnianie biblioteki sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-335">Distribute a health check library</span></span>

<span data-ttu-id="cc27b-336">Aby rozpowszechniać sprawdzanie kondycji jako bibliotekę:</span><span class="sxs-lookup"><span data-stu-id="cc27b-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="cc27b-337">Napisz sprawdzenie kondycji, które <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementuje interfejs jako klasę autonomiczną.</span><span class="sxs-lookup"><span data-stu-id="cc27b-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="cc27b-338">Klasa może polegać na [iniekcji zależności (DI),](xref:fundamentals/dependency-injection)aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) dostępu do danych konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="cc27b-339">W logice kontroli `CheckHealthAsync`kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="cc27b-340">`data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.</span><span class="sxs-lookup"><span data-stu-id="cc27b-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="cc27b-341">`AccessViolationException`obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="cc27b-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="cc27b-342">Gdy <xref:System.AccessViolationException> wystąpi, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> jest zwracany <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> z, aby umożliwić użytkownikom skonfigurowanie stanu awarii kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="cc27b-343">Napisz metodę rozszerzenia z parametrami, które `Startup.Configure` zużywająca aplikacja wywołuje w swojej metodzie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="cc27b-344">W poniższym przykładzie załóżmy, że następująca metoda sprawdzania kondycji podpisu:</span><span class="sxs-lookup"><span data-stu-id="cc27b-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="cc27b-345">Poprzedni podpis wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetwarzania logiki sondy sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="cc27b-346">Dane są dostarczane do delegata używane do tworzenia wystąpienia sprawdzania kondycji, gdy sprawdzanie kondycji jest zarejestrowany za pomocą metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="cc27b-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="cc27b-347">W poniższym przykładzie wywołujący określa opcjonalne:</span><span class="sxs-lookup"><span data-stu-id="cc27b-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="cc27b-348">nazwa kontroli`name`kondycji ( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-348">health check name (`name`).</span></span> <span data-ttu-id="cc27b-349">Jeśli `null` `example_health_check` , jest używany.</span><span class="sxs-lookup"><span data-stu-id="cc27b-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="cc27b-350">punkt danych ciągu dla`data1`kontroli kondycji ( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="cc27b-351">punktu danych dla kontroli kondycji`data2`( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="cc27b-352">Jeśli `null` `1` , jest używany.</span><span class="sxs-lookup"><span data-stu-id="cc27b-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="cc27b-353">stan awarii<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="cc27b-354">Wartość domyślna to `null`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-354">The default is `null`.</span></span> <span data-ttu-id="cc27b-355">Jeśli `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane dla stanu błędu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="cc27b-356">tagi`IEnumerable<string>`( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-356">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="cc27b-357">Wydawca sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-357">Health Check Publisher</span></span>

<span data-ttu-id="cc27b-358">Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system sprawdzania kondycji okresowo wykonuje `PublishAsync` kontrole kondycji i wywołuje z wynikiem.</span><span class="sxs-lookup"><span data-stu-id="cc27b-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="cc27b-359">Jest to przydatne w scenariuszu systemu monitorowania kondycji opartego na wypychach, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="cc27b-360">Interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ma jedną metodę:</span><span class="sxs-lookup"><span data-stu-id="cc27b-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="cc27b-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umożliwia ustawienie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="cc27b-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Początkowe opóźnienie zastosowane po uruchomieniu aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> przed wykonaniem wystąpień.</span><span class="sxs-lookup"><span data-stu-id="cc27b-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="cc27b-363">Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="cc27b-364">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-364">The default value is five seconds.</span></span>
* <span data-ttu-id="cc27b-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> realizacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="cc27b-366">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="cc27b-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` jest (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="cc27b-368">Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która filtruje zestaw kontroli.</span><span class="sxs-lookup"><span data-stu-id="cc27b-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="cc27b-369">Predykat jest oceniany w każdym okresie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="cc27b-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Limit czasu wykonywania kontroli kondycji dla <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wszystkich wystąpień.</span><span class="sxs-lookup"><span data-stu-id="cc27b-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="cc27b-371">Służy <xref:System.Threading.Timeout.InfiniteTimeSpan> do wykonywania bez limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="cc27b-372">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="cc27b-373">W przykładowej `ReadinessPublisher` aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> jest implementacja.</span><span class="sxs-lookup"><span data-stu-id="cc27b-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="cc27b-374">Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia na poziomie dziennika:</span><span class="sxs-lookup"><span data-stu-id="cc27b-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="cc27b-375">Informacje<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>kontroli kondycji jest .</span><span class="sxs-lookup"><span data-stu-id="cc27b-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="cc27b-376">Błąd<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>jest albo lub .</span><span class="sxs-lookup"><span data-stu-id="cc27b-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="cc27b-377">W przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa sekundy opóźnienia uruchamiania `LivenessProbeStartup` i uruchamia sprawdzanie co 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="cc27b-378">Aby aktywować <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, `ReadinessPublisher` przykład rejestruje jako usługę singleton w kontenerze [iniekcji zależności (DI):](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="cc27b-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="cc27b-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców kilku systemów, w tym [application insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="cc27b-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="cc27b-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="cc27b-381">Ograniczanie kontroli kondycji za pomocą mapWhen</span><span class="sxs-lookup"><span data-stu-id="cc27b-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="cc27b-382">Służy <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> do warunkowego rozgałęzienia potoku żądań dla punktów końcowych sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="cc27b-383">W poniższym `MapWhen` przykładzie odgałęzia potok żądania, aby aktywować `api/HealthCheck` oprogramowanie pośredniczące kontroli kondycji, jeśli żądanie GET zostanie odebrane dla punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="cc27b-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="cc27b-384">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="cc27b-384">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc27b-385">ASP.NET Core oferuje oprogramowanie pośredniczące i biblioteki health checks do raportowania kondycji składników infrastruktury aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="cc27b-386">Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc27b-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="cc27b-387">Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:</span><span class="sxs-lookup"><span data-stu-id="cc27b-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="cc27b-388">Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="cc27b-389">Na przykład orchestrator kontenera może odpowiedzieć na sprawdzanie kondycji wada, zatrzymując wdrożenie stopniowe lub ponowne uruchomienie kontenera.</span><span class="sxs-lookup"><span data-stu-id="cc27b-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="cc27b-390">Moduł równoważenia obciążenia może reagować na złej aplikacji przez routing ruchu z dala od wystąpienia awarii do wystąpienia w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="cc27b-391">Korzystanie z pamięci, dysku i innych zasobów serwera fizycznego można monitorować pod kątem stanu zdrowia.</span><span class="sxs-lookup"><span data-stu-id="cc27b-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="cc27b-392">Kontrole kondycji można przetestować zależności aplikacji, takich jak bazy danych i punktów końcowych usługi zewnętrznej, aby potwierdzić dostępność i normalne funkcjonowanie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="cc27b-393">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cc27b-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cc27b-394">Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="cc27b-395">Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="cc27b-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="cc27b-396">Zobacz przykładowy plik *README.md* aplikacji i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc27b-397">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cc27b-397">Prerequisites</span></span>

<span data-ttu-id="cc27b-398">Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenerów w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="cc27b-399">Przed dodaniem kontroli kondycji do aplikacji, zdecydować, który system monitorowania do użycia.</span><span class="sxs-lookup"><span data-stu-id="cc27b-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="cc27b-400">System monitorowania określa, jakie typy kontroli kondycji do utworzenia i jak skonfigurować ich punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="cc27b-401">Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.AspNetCore.Diagnostics.HealthChecks.](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)</span><span class="sxs-lookup"><span data-stu-id="cc27b-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="cc27b-402">Przykładowa aplikacja udostępnia kod startowy, aby zademonstrować testy kondycji dla kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="cc27b-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="cc27b-403">Scenariusz [sondy bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="cc27b-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="cc27b-404">Scenariusz [sondy DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę `DbContext`danych przy użyciu ef core .</span><span class="sxs-lookup"><span data-stu-id="cc27b-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="cc27b-405">Aby eksplorować scenariusze bazy danych, przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="cc27b-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="cc27b-406">Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="cc27b-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="cc27b-407">Ma następujące odwołania do pakietu w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="cc27b-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="cc27b-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="cc27b-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="cc27b-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="cc27b-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="cc27b-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="cc27b-411">Inny scenariusz sprawdzania kondycji pokazuje, jak filtrować kontrole kondycji do portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="cc27b-412">Przykładowa aplikacja wymaga utworzenia pliku *Properties/launchSettings.json,* który zawiera adres URL zarządzania i port zarządzania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="cc27b-413">Aby uzyskać więcej informacji, zobacz [sekcję Filtruj według portów.](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="cc27b-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="cc27b-414">Podstawowa sonda kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-414">Basic health probe</span></span>

<span data-ttu-id="cc27b-415">W przypadku wielu aplikacji podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do przetwarzania żądań *(żywotność)* jest wystarczająca do odnajdowania stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="cc27b-416">Podstawowa konfiguracja rejestruje usługi sprawdzania kondycji i wywołuje oprogramowanie pośredniczące kontroli kondycji, aby odpowiedzieć w punkcie końcowym adresu URL z odpowiedzią kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="cc27b-417">Domyślnie nie są rejestrowane żadne określone kontrole kondycji w celu przetestowania określonej zależności lub podsystemu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="cc27b-418">Aplikacja jest uważana za w dobrej kondycji, jeśli jest w stanie odpowiedzieć na adres URL punktu końcowego kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="cc27b-419">Domyślny moduł zapisujący odpowiedzi<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapisuje stan ( ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, wskazując stan [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)</span><span class="sxs-lookup"><span data-stu-id="cc27b-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="cc27b-420">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-421">Dodaj punkt końcowy dla oprogramowania <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pośredniczącego kontroli `Startup.Configure`kondycji w potoku przetwarzania żądań .</span><span class="sxs-lookup"><span data-stu-id="cc27b-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="cc27b-422">W przykładowej aplikacji punkt końcowy oceny `/health` kondycji jest tworzony w (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc27b-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="cc27b-423">Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="cc27b-424">Przykład platformy Docker</span><span class="sxs-lookup"><span data-stu-id="cc27b-424">Docker example</span></span>

<span data-ttu-id="cc27b-425">[Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, która może służyć do sprawdzania stanu aplikacji, która używa podstawowej konfiguracji sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="cc27b-426">Tworzenie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-426">Create health checks</span></span>

<span data-ttu-id="cc27b-427">Kontrole kondycji są tworzone <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> przez implementowanie interfejsu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="cc27b-428">Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zwraca, który wskazuje kondycję `Degraded`jako `Unhealthy` `Healthy`, lub .</span><span class="sxs-lookup"><span data-stu-id="cc27b-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="cc27b-429">Wynik jest zapisywany jako odpowiedź w postaci zwykłego tekstu z konfigurowalnym kodem stanu (konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji).](#health-check-options)</span><span class="sxs-lookup"><span data-stu-id="cc27b-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="cc27b-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwrócić opcjonalne pary klucz-wartość.</span><span class="sxs-lookup"><span data-stu-id="cc27b-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="cc27b-431">Przykładowa kontrola kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-431">Example health check</span></span>

<span data-ttu-id="cc27b-432">W `ExampleHealthCheck` poniższej klasie przedstawiono układ kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="cc27b-433">Logika kontroli kondycji jest `CheckHealthAsync` umieszczana w metodzie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="cc27b-434">W poniższym przykładzie ustawia `healthCheckResultHealthy`zmienną manekina, na . `true`</span><span class="sxs-lookup"><span data-stu-id="cc27b-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="cc27b-435">Jeśli wartość `healthCheckResultHealthy` jest ustawiona na `false`, [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stan jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="cc27b-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="cc27b-436">Zarejestruj usługi sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-436">Register health check services</span></span>

<span data-ttu-id="cc27b-437">Typ `ExampleHealthCheck` jest dodawany do `Startup.ConfigureServices` usług <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>sprawdzania kondycji w:</span><span class="sxs-lookup"><span data-stu-id="cc27b-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="cc27b-438">Przeciążenie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> pokazane w poniższym przykładzie<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>ustawia stan błędu ( ) do raportu, gdy sprawdzanie kondycji zgłasza błąd.</span><span class="sxs-lookup"><span data-stu-id="cc27b-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="cc27b-439">Jeśli stan błędu jest `null` ustawiony na (domyślnie), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane.</span><span class="sxs-lookup"><span data-stu-id="cc27b-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="cc27b-440">To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan awarii wskazany przez bibliotekę jest wymuszany przez aplikację, gdy wystąpi błąd sprawdzania kondycji, jeśli implementacja sprawdzania kondycji honoruje to ustawienie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="cc27b-441">*Tagi* mogą służyć do filtrowania kontroli kondycji (opisane dalej w [sekcji Filtrowanie kontroli kondycji).](#filter-health-checks)</span><span class="sxs-lookup"><span data-stu-id="cc27b-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="cc27b-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda.</span><span class="sxs-lookup"><span data-stu-id="cc27b-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="cc27b-443">W poniższym `Startup.ConfigureServices` przykładzie nazwa sprawdzania `Example` kondycji jest określona jako i sprawdzanie zawsze zwraca stan dobrej kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="cc27b-444">Korzystanie z oprogramowania pośredniczącego w zakresie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="cc27b-445">W `Startup.Configure`, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> wywołać w potoku przetwarzania z adresem URL punktu końcowego lub ścieżki względnej:</span><span class="sxs-lookup"><span data-stu-id="cc27b-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="cc27b-446">Jeśli kontrole kondycji powinny nasłuchiwać określonego portu, użyj przeciążenia, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> aby ustawić port (opisany dalej w sekcji Filtr według [portu):](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="cc27b-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="cc27b-447">Opcje sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-447">Health check options</span></span>

<span data-ttu-id="cc27b-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="cc27b-449">Sprawdzanie kondycji filtrowania</span><span class="sxs-lookup"><span data-stu-id="cc27b-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="cc27b-450">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="cc27b-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="cc27b-451">Pomijanie nagłówków pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="cc27b-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="cc27b-452">Dostosowywanie danych wyjściowych</span><span class="sxs-lookup"><span data-stu-id="cc27b-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="cc27b-453">Sprawdzanie kondycji filtrowania</span><span class="sxs-lookup"><span data-stu-id="cc27b-453">Filter health checks</span></span>

<span data-ttu-id="cc27b-454">Domyślnie oprogramowanie pośredniczące health checks uruchamia wszystkie zarejestrowane kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="cc27b-455">Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która zwraca wartość logiczną <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> do opcji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="cc27b-456">W poniższym przykładzie `Bar` sprawdzanie kondycji jest odfiltrowywało według jego znacznika (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracane tylko wtedy, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> właściwość sprawdzania kondycji jest zgodna `foo_tag` lub: `baz_tag`</span><span class="sxs-lookup"><span data-stu-id="cc27b-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="cc27b-457">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="cc27b-457">Customize the HTTP status code</span></span>

<span data-ttu-id="cc27b-458">Służy <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> do dostosowywania mapowania stanu kondycji do kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc27b-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="cc27b-459">Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="cc27b-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="cc27b-460">Zmień wartości kodu stanu, aby spełnić wymagania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="cc27b-461">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cc27b-461">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="cc27b-462">Pomijanie nagłówków pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="cc27b-462">Suppress cache headers</span></span>

<span data-ttu-id="cc27b-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>określa, czy oprogramowanie pośredniczące kontroli kondycji dodaje nagłówki HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cc27b-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="cc27b-464">Jeśli wartość `false` jest (domyślna), oprogramowanie pośredniczące `Cache-Control` `Expires`ustawia `Pragma` lub zastępuje , i nagłówki, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cc27b-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="cc27b-465">Jeśli wartość `true`jest , oprogramowanie pośredniczące nie modyfikuje nagłówki pamięci podręcznej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cc27b-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="cc27b-466">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cc27b-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="cc27b-467">Dostosowywanie danych wyjściowych</span><span class="sxs-lookup"><span data-stu-id="cc27b-467">Customize output</span></span>

<span data-ttu-id="cc27b-468">Opcja <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> pobiera lub ustawia delegata używanego do pisania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cc27b-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="cc27b-469">Domyślny delegat zapisuje minimalną odpowiedź w postaci zwykłego tekstu z wartością ciągu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="cc27b-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="cc27b-470">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cc27b-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="cc27b-471">Domyślny delegat zapisuje minimalną odpowiedź w postaci zwykłego tekstu z wartością ciągu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="cc27b-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="cc27b-472">Następujący delegat niestandardowy `WriteResponse`, wyprowadza niestandardową odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="cc27b-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="cc27b-473">System kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów zwracanych JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="cc27b-474">Możesz dostosować w `JObject` poprzednim przykładzie, gdy jest to konieczne do twoich potrzeb.</span><span class="sxs-lookup"><span data-stu-id="cc27b-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="cc27b-475">Sonda bazy danych</span><span class="sxs-lookup"><span data-stu-id="cc27b-475">Database probe</span></span>

<span data-ttu-id="cc27b-476">Sprawdzanie kondycji można określić kwerendę bazy danych do uruchomienia jako test logiczny, aby wskazać, czy baza danych odpowiada normalnie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="cc27b-477">Przykładowa aplikacja używa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, do sprawdzania kondycji w bazie danych programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cc27b-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="cc27b-478">`AspNetCore.Diagnostics.HealthChecks`wykonuje kwerendę do `SELECT 1` bazy danych, aby potwierdzić, że połączenie z bazą danych jest w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="cc27b-479">Podczas sprawdzania połączenia bazy danych z kwerendą wybierz kwerendę, która zwraca szybko.</span><span class="sxs-lookup"><span data-stu-id="cc27b-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="cc27b-480">Podejście zapytania ryzykuje przeciążenie bazy danych i obniżenie jej wydajności.</span><span class="sxs-lookup"><span data-stu-id="cc27b-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="cc27b-481">W większości przypadków uruchamianie kwerendy testowej nie jest konieczne.</span><span class="sxs-lookup"><span data-stu-id="cc27b-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="cc27b-482">Wystarczy jedynie pomyślne połączenie z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="cc27b-483">Jeśli okaże się konieczne uruchomienie kwerendy, wybierz proste `SELECT 1`zapytanie SELECT, takie jak .</span><span class="sxs-lookup"><span data-stu-id="cc27b-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="cc27b-484">Dołącz odwołanie do pakietu [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="cc27b-485">Podaj prawidłowy ciąg połączenia bazy danych w pliku *appsettings.json* przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="cc27b-486">Aplikacja korzysta z bazy danych `HealthCheckSample`programu SQL Server o nazwie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="cc27b-487">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-488">Przykładowa aplikacja `AddSqlServer` wywołuje metodę z ciągiem połączenia bazy danych *(DbHealthStartup.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="cc27b-489">Wywołanie health checks Middleware w `Startup.Configure`potoku przetwarzania aplikacji w:</span><span class="sxs-lookup"><span data-stu-id="cc27b-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="cc27b-490">Aby uruchomić scenariusz sondy bazy danych przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="cc27b-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="cc27b-492">Sonda Core DbContext programu Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cc27b-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="cc27b-493">Sprawdzanie `DbContext` potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla ef core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="cc27b-494">Sprawdzanie `DbContext` jest obsługiwane w aplikacjach, które:</span><span class="sxs-lookup"><span data-stu-id="cc27b-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="cc27b-495">Użyj [core (Entity Framework) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="cc27b-496">Dołącz odwołanie do pakietu [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="cc27b-497">`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="cc27b-498">Jest `DbContext` dostarczany jako `TContext` do metody.</span><span class="sxs-lookup"><span data-stu-id="cc27b-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="cc27b-499">Przeciążenie jest dostępne do skonfigurowania stanu błędu, tagów i niestandardowej kwerendy testowej.</span><span class="sxs-lookup"><span data-stu-id="cc27b-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="cc27b-500">Domyślnie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-500">By default:</span></span>

* <span data-ttu-id="cc27b-501">Wywołanie `DbContextHealthCheck` metody EF `CanConnectAsync` Core.</span><span class="sxs-lookup"><span data-stu-id="cc27b-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="cc27b-502">Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metody.</span><span class="sxs-lookup"><span data-stu-id="cc27b-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="cc27b-503">Nazwa sprawdzania kondycji jest nazwą `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="cc27b-504">W przykładowej `AppDbContext` aplikacji, `AddDbContextCheck` jest dostarczany i `Startup.ConfigureServices` zarejestrowany jako usługa w (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc27b-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="cc27b-505">W przykładowej `UseHealthChecks` aplikacji dodaje oprogramowanie pośredniczące kontroli kondycji w `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="cc27b-506">Aby uruchomić `DbContext` scenariusz sondowania przy użyciu przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cc27b-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="cc27b-507">Jeśli baza danych istnieje, usuń ją.</span><span class="sxs-lookup"><span data-stu-id="cc27b-507">If the database exists, delete it.</span></span>

<span data-ttu-id="cc27b-508">Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="cc27b-509">Po uruchomieniu aplikacji sprawdź stan kondycji, wysyłając żądanie `/health` do punktu końcowego w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="cc27b-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="cc27b-510">Baza danych `AppDbContext` i nie istnieje, więc aplikacja zapewnia następującą odpowiedź:</span><span class="sxs-lookup"><span data-stu-id="cc27b-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="cc27b-511">Wyzwalanie przykładowej aplikacji, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="cc27b-512">Złożyć wniosek `/createdatabase`do .</span><span class="sxs-lookup"><span data-stu-id="cc27b-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="cc27b-513">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="cc27b-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="cc27b-514">Złożyć żądanie `/health` do punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="cc27b-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="cc27b-515">Istnieje baza danych i kontekst, więc aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="cc27b-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="cc27b-516">Wyzwalanie przykładowej aplikacji, aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="cc27b-517">Złożyć wniosek `/deletedatabase`do .</span><span class="sxs-lookup"><span data-stu-id="cc27b-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="cc27b-518">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="cc27b-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="cc27b-519">Złożyć żądanie `/health` do punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="cc27b-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="cc27b-520">Aplikacja zapewnia w złej kondycji odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="cc27b-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="cc27b-521">Oddzielne sondy gotowości i żywotności</span><span class="sxs-lookup"><span data-stu-id="cc27b-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="cc27b-522">W niektórych scenariuszach hostingu używana jest para kontroli kondycji, które rozróżniają dwa stany aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="cc27b-523">Aplikacja działa, ale nie jest jeszcze gotowa do odbierania żądań.</span><span class="sxs-lookup"><span data-stu-id="cc27b-523">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="cc27b-524">Ten stan jest *gotowość*aplikacji .</span><span class="sxs-lookup"><span data-stu-id="cc27b-524">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="cc27b-525">Aplikacja działa i odpowiada na żądania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-525">The app is functioning and responding to requests.</span></span> <span data-ttu-id="cc27b-526">Ten stan jest *żywość*aplikacji .</span><span class="sxs-lookup"><span data-stu-id="cc27b-526">This state is the app's *liveness*.</span></span>

<span data-ttu-id="cc27b-527">Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli, aby ustalić, czy wszystkie podsystemy i zasoby aplikacji są dostępne.</span><span class="sxs-lookup"><span data-stu-id="cc27b-527">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="cc27b-528">Sprawdzanie żywotności wykonuje tylko szybkie sprawdzenie, aby ustalić, czy aplikacja jest dostępna do przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="cc27b-528">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="cc27b-529">Po przejściu przez aplikację kontroli gotowości, nie ma potrzeby, aby obciążyć&mdash;aplikację dalej z drogim zestawem kontroli gotowości dalsze kontrole wymagają tylko sprawdzenia, czy nie ma żywotności.</span><span class="sxs-lookup"><span data-stu-id="cc27b-529">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="cc27b-530">Przykładowa aplikacja zawiera sprawdzanie kondycji, aby zgłosić zakończenie długotrwałego zadania uruchamiania w [usłudze hosted](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="cc27b-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="cc27b-531">Udostępnia `StartupHostedServiceHealthCheck` właściwość, `StartupTaskCompleted`że usługa hostowana można `true` ustawić po zakończeniu jej długotrwałe zadanie *(StartupHostedServiceHealthCheck.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="cc27b-532">Długotrwałe zadanie w tle jest uruchamiane przez [usługę hostowana](xref:fundamentals/host/hosted-services) *(Usługi/StartupHostedService).*</span><span class="sxs-lookup"><span data-stu-id="cc27b-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="cc27b-533">Po zakończeniu zadania, `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony `true`na:</span><span class="sxs-lookup"><span data-stu-id="cc27b-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="cc27b-534">Kontrola kondycji jest <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zarejestrowana w `Startup.ConfigureServices` wraz z usługą hosta.</span><span class="sxs-lookup"><span data-stu-id="cc27b-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="cc27b-535">Ponieważ usługa hostowana musi ustawić właściwość na kontroli kondycji, kontrola kondycji jest również zarejestrowana w kontenerze serwisowym (*LivenessProbeStartup.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="cc27b-536">Wywołanie health checks Middleware w `Startup.Configure`potoku przetwarzania aplikacji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="cc27b-537">W przykładowej aplikacji punkty końcowe sprawdzania `/health/ready` kondycji są tworzone `/health/live` w celu sprawdzenia gotowości i sprawdzania żywotności.</span><span class="sxs-lookup"><span data-stu-id="cc27b-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="cc27b-538">Sprawdzanie gotowości filtruje kontrole kondycji do sprawdzenia `ready` kondycji za pomocą tagu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="cc27b-539">Sprawdzanie żywotności filtruje `StartupHostedServiceHealthCheck` przez `false` powrót w [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (aby uzyskać więcej informacji, zobacz [filtr kontroli kondycji):](#filter-health-checks)</span><span class="sxs-lookup"><span data-stu-id="cc27b-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="cc27b-540">Aby uruchomić scenariusz konfiguracji gotowości/żywotności przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="cc27b-541">W przeglądarce `/health/ready` odwiedź kilka razy, aż minęło 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="cc27b-542">Kontrola kondycji raportuje *wzdrowy sposób* przez pierwsze 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="cc27b-543">Po 15 sekundach punkt końcowy raportuje *w dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania przez hostowany serwis.</span><span class="sxs-lookup"><span data-stu-id="cc27b-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="cc27b-544">W tym przykładzie tworzy<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> również Health Check Publisher (implementacja), który uruchamia pierwsze sprawdzanie gotowości z dwusekundowym opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="cc27b-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="cc27b-545">Aby uzyskać więcej informacji, zobacz sekcję [Wydawca sprawdzania kondycji.](#health-check-publisher)</span><span class="sxs-lookup"><span data-stu-id="cc27b-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="cc27b-546">Przykład Kubernetes</span><span class="sxs-lookup"><span data-stu-id="cc27b-546">Kubernetes example</span></span>

<span data-ttu-id="cc27b-547">Korzystanie z oddzielnych kontroli gotowości i żywotności jest przydatne w środowisku takim jak [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="cc27b-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="cc27b-548">W umięsierze aplikacja może być wymagana do wykonywania czasochłonnych prac podczas uruchamiania przed zaakceptowaniem żądań, takich jak test podstawowej dostępności bazy danych.</span><span class="sxs-lookup"><span data-stu-id="cc27b-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="cc27b-549">Za pomocą oddzielnych kontroli umożliwia orchestrator odróżnić, czy aplikacja działa, ale nie jest jeszcze gotowy lub jeśli aplikacja nie powiodło się uruchomić.</span><span class="sxs-lookup"><span data-stu-id="cc27b-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="cc27b-550">Aby uzyskać więcej informacji na temat gotowości i sondy żywotności w umięsienia, zobacz [Konfigurowanie sondy żywotności i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji programu Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="cc27b-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="cc27b-551">W poniższym przykładzie pokazano konfigurację sondy gotowości Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="cc27b-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="cc27b-552">Sonda oparta na metrykach z modułem zapisu odpowiedzi niestandardowej</span><span class="sxs-lookup"><span data-stu-id="cc27b-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="cc27b-553">Przykładowa aplikacja pokazuje sprawdzanie kondycji pamięci za pomocą modułu zapisującego odpowiedzi niestandardowej.</span><span class="sxs-lookup"><span data-stu-id="cc27b-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="cc27b-554">`MemoryHealthCheck`zgłasza stan w złej kondycji, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji).</span><span class="sxs-lookup"><span data-stu-id="cc27b-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="cc27b-555">Informacje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> o odpadach odśmiecania (GC) dla aplikacji *(MemoryHealthCheck.cs):*</span><span class="sxs-lookup"><span data-stu-id="cc27b-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="cc27b-556">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-557">Zamiast włączać sprawdzanie kondycji, przekazując <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>go `MemoryHealthCheck` do , jest zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="cc27b-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="cc27b-558">Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług kontroli kondycji i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="cc27b-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="cc27b-559">Zalecamy rejestrowanie usług kontroli kondycji jako usług Singleton.</span><span class="sxs-lookup"><span data-stu-id="cc27b-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="cc27b-560">W przykładowej aplikacji (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc27b-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="cc27b-561">Wywołanie health checks Middleware w `Startup.Configure`potoku przetwarzania aplikacji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="cc27b-562">Pełnomocnik `WriteResponse` jest dostarczany `ResponseWriter` do właściwości do wysuń niestandardową odpowiedź JSON podczas wykonywania sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="cc27b-563">Metoda `WriteResponse` formatuje `CompositeHealthCheckResult` do obiektu JSON i daje wyjście JSON dla odpowiedzi na sprawdzenie kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="cc27b-564">Aby uruchomić sondę opartą na metrykach z niestandardowym wyjściem modułu zapisującego odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="cc27b-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zawiera scenariusze sprawdzania kondycji oparte na metrykach, w tym sprawdzanie żywotności dysku i maksymalnej wartości.</span><span class="sxs-lookup"><span data-stu-id="cc27b-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="cc27b-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="cc27b-567">Filtrowanie według portów</span><span class="sxs-lookup"><span data-stu-id="cc27b-567">Filter by port</span></span>

<span data-ttu-id="cc27b-568">Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> z portem ogranicza żądania sprawdzania kondycji do określonego portu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="cc27b-569">Jest to zwykle używane w środowisku kontenera, aby udostępnić port dla usług monitorowania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="cc27b-570">Przykładowa aplikacja konfiguruje port przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cc27b-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="cc27b-571">Port jest ustawiony w pliku *launchSettings.json* i przekazywany do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="cc27b-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="cc27b-572">Należy również skonfigurować serwer do nasłuchiwać żądań na porcie zarządzania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="cc27b-573">Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portu zarządzania, utwórz plik *launchSettings.json* w folderze *Właściwości.*</span><span class="sxs-lookup"><span data-stu-id="cc27b-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="cc27b-574">Następujący plik *Properties/launchSettings.json* w przykładowej aplikacji nie jest uwzględniony w przykładowych plikach projektu aplikacji i musi zostać utworzony ręcznie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="cc27b-575">Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku .</span><span class="sxs-lookup"><span data-stu-id="cc27b-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc27b-576">Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> określa port zarządzania (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc27b-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="cc27b-577">Można uniknąć tworzenia *pliku launchSettings.json* w przykładowej aplikacji, ustawiając adresy URL i port zarządzania jawnie w kodzie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="cc27b-578">W *Program.cs,* w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> którym jest tworzony, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> dodaj wywołanie i podaj normalnego punktu końcowego odpowiedzi aplikacji i punktu końcowego portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="cc27b-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="cc27b-579">W *ManagementPortStartup.cs* gdzie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> jest wywoływana, należy określić port zarządzania jawnie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="cc27b-580">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="cc27b-580">*Program.cs*:</span></span>
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
> <span data-ttu-id="cc27b-581">*ManagementPortStartup.cs:*</span><span class="sxs-lookup"><span data-stu-id="cc27b-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="cc27b-582">Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cc27b-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="cc27b-583">Rozpowszechnianie biblioteki sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-583">Distribute a health check library</span></span>

<span data-ttu-id="cc27b-584">Aby rozpowszechniać sprawdzanie kondycji jako bibliotekę:</span><span class="sxs-lookup"><span data-stu-id="cc27b-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="cc27b-585">Napisz sprawdzenie kondycji, które <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementuje interfejs jako klasę autonomiczną.</span><span class="sxs-lookup"><span data-stu-id="cc27b-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="cc27b-586">Klasa może polegać na [iniekcji zależności (DI),](xref:fundamentals/dependency-injection)aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) dostępu do danych konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="cc27b-587">W logice kontroli `CheckHealthAsync`kondycji:</span><span class="sxs-lookup"><span data-stu-id="cc27b-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="cc27b-588">`data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.</span><span class="sxs-lookup"><span data-stu-id="cc27b-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="cc27b-589">`AccessViolationException`obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="cc27b-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="cc27b-590">Gdy <xref:System.AccessViolationException> wystąpi, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> jest zwracany <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> z, aby umożliwić użytkownikom skonfigurowanie stanu awarii kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="cc27b-591">Napisz metodę rozszerzenia z parametrami, które `Startup.Configure` zużywająca aplikacja wywołuje w swojej metodzie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="cc27b-592">W poniższym przykładzie załóżmy, że następująca metoda sprawdzania kondycji podpisu:</span><span class="sxs-lookup"><span data-stu-id="cc27b-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="cc27b-593">Poprzedni podpis wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetwarzania logiki sondy sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="cc27b-594">Dane są dostarczane do delegata używane do tworzenia wystąpienia sprawdzania kondycji, gdy sprawdzanie kondycji jest zarejestrowany za pomocą metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="cc27b-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="cc27b-595">W poniższym przykładzie wywołujący określa opcjonalne:</span><span class="sxs-lookup"><span data-stu-id="cc27b-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="cc27b-596">nazwa kontroli`name`kondycji ( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-596">health check name (`name`).</span></span> <span data-ttu-id="cc27b-597">Jeśli `null` `example_health_check` , jest używany.</span><span class="sxs-lookup"><span data-stu-id="cc27b-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="cc27b-598">punkt danych ciągu dla`data1`kontroli kondycji ( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="cc27b-599">punktu danych dla kontroli kondycji`data2`( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="cc27b-600">Jeśli `null` `1` , jest używany.</span><span class="sxs-lookup"><span data-stu-id="cc27b-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="cc27b-601">stan awarii<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="cc27b-602">Wartość domyślna to `null`.</span><span class="sxs-lookup"><span data-stu-id="cc27b-602">The default is `null`.</span></span> <span data-ttu-id="cc27b-603">Jeśli `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane dla stanu błędu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="cc27b-604">tagi`IEnumerable<string>`( ).</span><span class="sxs-lookup"><span data-stu-id="cc27b-604">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="cc27b-605">Wydawca sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="cc27b-605">Health Check Publisher</span></span>

<span data-ttu-id="cc27b-606">Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system sprawdzania kondycji okresowo wykonuje `PublishAsync` kontrole kondycji i wywołuje z wynikiem.</span><span class="sxs-lookup"><span data-stu-id="cc27b-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="cc27b-607">Jest to przydatne w scenariuszu systemu monitorowania kondycji opartego na wypychach, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="cc27b-608">Interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ma jedną metodę:</span><span class="sxs-lookup"><span data-stu-id="cc27b-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="cc27b-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umożliwia ustawienie:</span><span class="sxs-lookup"><span data-stu-id="cc27b-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="cc27b-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Początkowe opóźnienie zastosowane po uruchomieniu aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> przed wykonaniem wystąpień.</span><span class="sxs-lookup"><span data-stu-id="cc27b-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="cc27b-611">Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="cc27b-612">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-612">The default value is five seconds.</span></span>
* <span data-ttu-id="cc27b-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> realizacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="cc27b-614">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="cc27b-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` jest (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="cc27b-616">Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która filtruje zestaw kontroli.</span><span class="sxs-lookup"><span data-stu-id="cc27b-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="cc27b-617">Predykat jest oceniany w każdym okresie.</span><span class="sxs-lookup"><span data-stu-id="cc27b-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="cc27b-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Limit czasu wykonywania kontroli kondycji dla <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wszystkich wystąpień.</span><span class="sxs-lookup"><span data-stu-id="cc27b-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="cc27b-619">Służy <xref:System.Threading.Timeout.InfiniteTimeSpan> do wykonywania bez limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="cc27b-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="cc27b-620">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="cc27b-621">W wydaniu ASP.NET Core 2.2 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> ustawienie nie jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> honorowane przez implementację; ustawia wartość . <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay></span><span class="sxs-lookup"><span data-stu-id="cc27b-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="cc27b-622">Ten problem został rozwiązany w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="cc27b-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="cc27b-623">W przykładowej `ReadinessPublisher` aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> jest implementacja.</span><span class="sxs-lookup"><span data-stu-id="cc27b-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="cc27b-624">Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="cc27b-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="cc27b-625">Informacje<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>kontroli kondycji jest .</span><span class="sxs-lookup"><span data-stu-id="cc27b-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="cc27b-626">Błąd<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>jest albo lub .</span><span class="sxs-lookup"><span data-stu-id="cc27b-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="cc27b-627">W przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa sekundy opóźnienia uruchamiania `LivenessProbeStartup` i uruchamia sprawdzanie co 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="cc27b-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="cc27b-628">Aby aktywować <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, `ReadinessPublisher` przykład rejestruje jako usługę singleton w kontenerze [iniekcji zależności (DI):](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="cc27b-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="cc27b-629">Poniższe obejście zezwala <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> na dodanie wystąpienia do kontenera usługi, gdy jedna lub więcej innych usług hostowanych zostało już dodanych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="cc27b-630">To obejście nie jest wymagane w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="cc27b-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="cc27b-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców kilku systemów, w tym [application insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="cc27b-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="cc27b-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc27b-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="cc27b-633">Ograniczanie kontroli kondycji za pomocą mapWhen</span><span class="sxs-lookup"><span data-stu-id="cc27b-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="cc27b-634">Służy <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> do warunkowego rozgałęzienia potoku żądań dla punktów końcowych sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="cc27b-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="cc27b-635">W poniższym `MapWhen` przykładzie odgałęzia potok żądania, aby aktywować `api/HealthCheck` oprogramowanie pośredniczące kontroli kondycji, jeśli żądanie GET zostanie odebrane dla punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="cc27b-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="cc27b-636">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="cc27b-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
