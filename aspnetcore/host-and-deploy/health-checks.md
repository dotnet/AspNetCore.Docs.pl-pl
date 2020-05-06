---
title: Kontrole kondycji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować Sprawdzanie kondycji infrastruktury ASP.NET Core, na przykład aplikacje i bazy danych.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/health-checks
ms.openlocfilehash: 67ba564068e8fba320ee04a59721052075337c65
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776067"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="31248-103">Kontrole kondycji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31248-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="31248-104">Autor [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="31248-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31248-105">ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="31248-106">Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP.</span><span class="sxs-lookup"><span data-stu-id="31248-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="31248-107">Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:</span><span class="sxs-lookup"><span data-stu-id="31248-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="31248-108">Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="31248-109">Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera.</span><span class="sxs-lookup"><span data-stu-id="31248-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="31248-110">Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="31248-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="31248-111">Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.</span><span class="sxs-lookup"><span data-stu-id="31248-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="31248-112">Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.</span><span class="sxs-lookup"><span data-stu-id="31248-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="31248-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="31248-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="31248-114">Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="31248-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="31248-115">Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="31248-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="31248-116">Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31248-117">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="31248-117">Prerequisites</span></span>

<span data-ttu-id="31248-118">Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="31248-119">Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany.</span><span class="sxs-lookup"><span data-stu-id="31248-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="31248-120">System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="31248-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="31248-121">Pakiet [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) jest wywoływany niejawnie dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="31248-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="31248-122">Aby przeprowadzić kontrolę kondycji przy użyciu Entity Framework Core, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="31248-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="31248-123">Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="31248-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="31248-124">Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="31248-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="31248-125">Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="31248-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="31248-126">Aby poznać scenariusze baz danych, przykładową aplikację:</span><span class="sxs-lookup"><span data-stu-id="31248-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="31248-127">Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="31248-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="31248-128">W pliku projektu znajdują się następujące odwołania do pakietów:</span><span class="sxs-lookup"><span data-stu-id="31248-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="31248-129">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="31248-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="31248-130">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="31248-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="31248-131">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="31248-132">W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="31248-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="31248-133">Przykładowa aplikacja wymaga utworzenia pliku *Properties/profilu launchsettings. JSON* , który zawiera adres URL zarządzania i port zarządzania.</span><span class="sxs-lookup"><span data-stu-id="31248-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="31248-134">Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="31248-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="31248-135">Podstawowa sonda kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-135">Basic health probe</span></span>

<span data-ttu-id="31248-136">W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania,*liveness*jest wystarczająca do odnajdywania stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="31248-137">Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję.</span><span class="sxs-lookup"><span data-stu-id="31248-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="31248-138">Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu.</span><span class="sxs-lookup"><span data-stu-id="31248-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="31248-139">Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="31248-140">Domyślny moduł zapisujący odpowiedzi zapisuje stan (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżone](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus stan złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="31248-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="31248-141">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-142">Utwórz punkt końcowy sprawdzania kondycji, `MapHealthChecks` wywołując `Startup.Configure`w.</span><span class="sxs-lookup"><span data-stu-id="31248-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="31248-143">W przykładowej aplikacji jest tworzony punkt końcowy sprawdzania kondycji `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="31248-144">Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="31248-145">Przykład platformy Docker</span><span class="sxs-lookup"><span data-stu-id="31248-145">Docker example</span></span>

<span data-ttu-id="31248-146">Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="31248-147">Utwórz kontrole kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-147">Create health checks</span></span>

<span data-ttu-id="31248-148">Kontrole kondycji są tworzone przez implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="31248-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="31248-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Metoda zwraca <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> wartość wskazującą kondycję jako `Healthy`, `Degraded`lub. `Unhealthy`</span><span class="sxs-lookup"><span data-stu-id="31248-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="31248-150">Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="31248-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="31248-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwracać opcjonalne pary klucz-wartość.</span><span class="sxs-lookup"><span data-stu-id="31248-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="31248-152">W poniższej `ExampleHealthCheck` klasie przedstawiono układ kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="31248-153">Logika kontroli kondycji jest umieszczana `CheckHealthAsync` w metodzie.</span><span class="sxs-lookup"><span data-stu-id="31248-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="31248-154">Poniższy przykład ustawia zmienną `healthCheckResultHealthy`fikcyjną, do. `true`</span><span class="sxs-lookup"><span data-stu-id="31248-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="31248-155">Jeśli wartość `healthCheckResultHealthy` jest równa `false`, zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="31248-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="31248-156">Rejestrowanie usług sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-156">Register health check services</span></span>

<span data-ttu-id="31248-157">`ExampleHealthCheck` Typ jest dodawany do usług sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w programie `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="31248-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="31248-158"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> Przeciążenie pokazane w poniższym przykładzie ustawia stan błędu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) w celu raportowania, kiedy Sprawdzanie kondycji zgłasza błąd.</span><span class="sxs-lookup"><span data-stu-id="31248-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="31248-159">Jeśli stan niepowodzenia jest ustawiony na `null` wartość (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="31248-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="31248-160">To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.</span><span class="sxs-lookup"><span data-stu-id="31248-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="31248-161">*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="31248-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="31248-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda.</span><span class="sxs-lookup"><span data-stu-id="31248-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="31248-163">W poniższym przykładzie nazwa sprawdzania kondycji jest określona jako `Example` , a sprawdzanie zawsze zwraca prawidłowy stan:</span><span class="sxs-lookup"><span data-stu-id="31248-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="31248-164">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> przekazywania argumentów do implementacji kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="31248-165">W poniższym przykładzie `TestHealthCheckWithArgs` akceptuje liczbę całkowitą i ciąg do użycia, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="31248-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="31248-166">`TestHealthCheckWithArgs`jest zarejestrowany przez wywołanie `AddTypeActivatedCheck` z liczbą całkowitą i ciągiem przekazaną do implementacji:</span><span class="sxs-lookup"><span data-stu-id="31248-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="31248-167">Użyj routingu kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-167">Use Health Checks Routing</span></span>

<span data-ttu-id="31248-168">W `Startup.Configure`programie Wywołaj `MapHealthChecks` program Endpoint Builder z adresem URL punktu końcowego lub ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="31248-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="31248-169">Wymagaj hosta</span><span class="sxs-lookup"><span data-stu-id="31248-169">Require host</span></span>

<span data-ttu-id="31248-170">Wywołaj `RequireHost` , aby określić co najmniej jeden dozwolony Host dla punktu końcowego sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="31248-171">Hosty powinny być w formacie Unicode, a nie formacie Punycode i mogą zawierać port.</span><span class="sxs-lookup"><span data-stu-id="31248-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="31248-172">Jeśli kolekcja nie zostanie podana, każdy host zostanie zaakceptowany.</span><span class="sxs-lookup"><span data-stu-id="31248-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="31248-173">Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="31248-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="31248-174">Wymagaj autoryzacji</span><span class="sxs-lookup"><span data-stu-id="31248-174">Require authorization</span></span>

<span data-ttu-id="31248-175">Wywołaj `RequireAuthorization` , aby uruchomić oprogramowanie pośredniczące autoryzacji w punkcie końcowym żądania sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="31248-176">`RequireAuthorization` Przeciążenie akceptuje co najmniej jedną zasadę autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="31248-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="31248-177">Jeśli nie podano zasad, zostanie użyta domyślna zasada autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="31248-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="31248-178">Włączanie żądań Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="31248-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="31248-179">Mimo że kontrole kondycji są wykonywane ręcznie z przeglądarki, nie jest to typowy scenariusz użycia, można włączyć oprogramowanie do `RequireCors` obsługi mechanizmu CORS, wywołując testy kondycji punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="31248-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="31248-180">`RequireCors` Przeciążenie akceptuje delegata konstruktora zasad CORS (`CorsPolicyBuilder`) lub nazwę zasady.</span><span class="sxs-lookup"><span data-stu-id="31248-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="31248-181">Jeśli nie podano zasad, zostanie użyta domyślna zasada CORS.</span><span class="sxs-lookup"><span data-stu-id="31248-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="31248-182">Aby uzyskać więcej informacji, zobacz <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="31248-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="31248-183">Opcje sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-183">Health check options</span></span>

<span data-ttu-id="31248-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="31248-185">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="31248-186">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="31248-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="31248-187">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="31248-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="31248-188">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="31248-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="31248-189">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-189">Filter health checks</span></span>

<span data-ttu-id="31248-190">Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="31248-191">Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną dla <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji.</span><span class="sxs-lookup"><span data-stu-id="31248-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="31248-192">W `Bar` poniższym przykładzie Sprawdzanie kondycji jest odfiltrowane przez tag (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> Właściwość sprawdzania kondycji jest zgodna `foo_tag` lub: `baz_tag`</span><span class="sxs-lookup"><span data-stu-id="31248-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="31248-193">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="31248-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="31248-194">W `Startup.Configure`programie program `Predicate` filtruje kontrolę kondycji "bar".</span><span class="sxs-lookup"><span data-stu-id="31248-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="31248-195">Tylko Foo i baz Execute.:</span><span class="sxs-lookup"><span data-stu-id="31248-195">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="31248-196">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="31248-196">Customize the HTTP status code</span></span>

<span data-ttu-id="31248-197">Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> , aby dostosować mapowanie stanu kondycji do kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="31248-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="31248-198">Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="31248-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="31248-199">Zmień wartości kodów stanu, aby spełniały Twoje wymagania.</span><span class="sxs-lookup"><span data-stu-id="31248-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="31248-200">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="31248-200">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="31248-201">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="31248-201">Suppress cache headers</span></span>

<span data-ttu-id="31248-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Określa, czy kontrole kondycji powodują dodanie nagłówków HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="31248-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="31248-203">Jeśli `false` wartość jest (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje `Cache-Control`nagłówki, `Expires`i `Pragma` , aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="31248-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="31248-204">Jeśli wartość to `true`, oprogramowanie pośredniczące nie modyfikuje nagłówków pamięci podręcznej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="31248-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="31248-205">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="31248-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="31248-206">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="31248-206">Customize output</span></span>

<span data-ttu-id="31248-207">W `Startup.Configure`programie Ustaw dla opcji [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) delegata na potrzeby zapisywania odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="31248-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="31248-208">Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="31248-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="31248-209">Następujące niestandardowe Delegaty wyprowadzają niestandardową odpowiedź JSON.</span><span class="sxs-lookup"><span data-stu-id="31248-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="31248-210">Pierwszy przykład z przykładowej aplikacji pokazuje, jak używać <xref:System.Text.Json?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="31248-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="31248-211">Drugi przykład ilustruje sposób użycia [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="31248-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="31248-212">W przykładowej aplikacji Dodaj `SYSTEM_TEXT_JSON` komentarz do [dyrektywy preprocesora](xref:index#preprocessor-directives-in-sample-code) w programie *CustomWriterStartup.cs* , aby włączyć `Newtonsoft.Json` wersję programu. `WriteResponse`</span><span class="sxs-lookup"><span data-stu-id="31248-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="31248-213">Interfejs API kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="31248-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="31248-214">W razie konieczności dostosuj odpowiedź w powyższych przykładach.</span><span class="sxs-lookup"><span data-stu-id="31248-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="31248-215">Aby uzyskać więcej informacji na temat serializacji `System.Text.Json`JSON przy użyciu, zobacz [jak serializować i deserializować kod JSON w programie .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="31248-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="31248-216">Sonda bazy danych</span><span class="sxs-lookup"><span data-stu-id="31248-216">Database probe</span></span>

<span data-ttu-id="31248-217">Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="31248-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="31248-218">Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych.</span><span class="sxs-lookup"><span data-stu-id="31248-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="31248-219">`AspNetCore.Diagnostics.HealthChecks`wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="31248-220">Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko.</span><span class="sxs-lookup"><span data-stu-id="31248-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="31248-221">Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności.</span><span class="sxs-lookup"><span data-stu-id="31248-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="31248-222">W większości przypadków uruchomienie zapytania testowego nie jest konieczne.</span><span class="sxs-lookup"><span data-stu-id="31248-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="31248-223">Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="31248-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="31248-224">Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="31248-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="31248-225">Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="31248-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="31248-226">Podaj prawidłowe parametry połączenia z bazą danych w pliku *appSettings. JSON* przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="31248-227">Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="31248-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="31248-228">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-229">Przykładowa aplikacja wywołuje `AddSqlServer` metodę z parametrami połączenia bazy danych (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="31248-230">Punkt końcowy sprawdzania kondycji jest tworzony przez `MapHealthChecks` wywołanie `Startup.Configure`w:</span><span class="sxs-lookup"><span data-stu-id="31248-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="31248-231">Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="31248-232">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="31248-233">Badanie Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="31248-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="31248-234">`DbContext` Sprawdzanie potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="31248-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="31248-235">`DbContext` Sprawdzanie jest obsługiwane w aplikacjach, które:</span><span class="sxs-lookup"><span data-stu-id="31248-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="31248-236">Użyj [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="31248-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="31248-237">Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="31248-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="31248-238">`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla elementu `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="31248-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="31248-239">`DbContext` Jest dostarczany jako `TContext` do metody.</span><span class="sxs-lookup"><span data-stu-id="31248-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="31248-240">Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.</span><span class="sxs-lookup"><span data-stu-id="31248-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="31248-241">Domyślnie:</span><span class="sxs-lookup"><span data-stu-id="31248-241">By default:</span></span>

* <span data-ttu-id="31248-242">`CanConnectAsync` Metoda `DbContextHealthCheck` wywołań EF Core.</span><span class="sxs-lookup"><span data-stu-id="31248-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="31248-243">Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metod.</span><span class="sxs-lookup"><span data-stu-id="31248-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="31248-244">Nazwa sprawdzania kondycji jest nazwą `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="31248-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="31248-245">`AppDbContext` W przykładowej aplikacji jest `AddDbContextCheck` dostarczany i zarejestrowany jako usługa w `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="31248-246">Punkt końcowy sprawdzania kondycji jest tworzony przez `MapHealthChecks` wywołanie `Startup.Configure`w:</span><span class="sxs-lookup"><span data-stu-id="31248-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="31248-247">Aby uruchomić scenariusz `DbContext` sondowania za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="31248-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="31248-248">Jeśli baza danych istnieje, usuń ją.</span><span class="sxs-lookup"><span data-stu-id="31248-248">If the database exists, delete it.</span></span>

<span data-ttu-id="31248-249">Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="31248-250">Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do `/health` punktu końcowego w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="31248-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="31248-251">Baza danych programu `AppDbContext` i nie istnieje, więc aplikacja udostępnia następujące odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="31248-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="31248-252">Wyzwól przykładową aplikację, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="31248-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="31248-253">Wprowadź żądanie do `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="31248-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="31248-254">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="31248-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="31248-255">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="31248-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="31248-256">Istnieje baza danych i kontekst, aby aplikacja odpowiadała:</span><span class="sxs-lookup"><span data-stu-id="31248-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="31248-257">Wyzwól przykładową aplikację, aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="31248-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="31248-258">Wprowadź żądanie do `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="31248-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="31248-259">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="31248-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="31248-260">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="31248-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="31248-261">Aplikacja zawiera odpowiedź w złej kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="31248-262">Oddzielne sondy gotowości i na żywo</span><span class="sxs-lookup"><span data-stu-id="31248-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="31248-263">W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:</span><span class="sxs-lookup"><span data-stu-id="31248-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="31248-264">Aplikacja działa, ale jeszcze nie jest gotowa do odbierania żądań.</span><span class="sxs-lookup"><span data-stu-id="31248-264">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="31248-265">Jest to stan *gotowości*aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-265">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="31248-266">Aplikacja działa i odpowiada na żądania.</span><span class="sxs-lookup"><span data-stu-id="31248-266">The app is functioning and responding to requests.</span></span> <span data-ttu-id="31248-267">Ten stan jest *aktywny*.</span><span class="sxs-lookup"><span data-stu-id="31248-267">This state is the app's *liveness*.</span></span>

<span data-ttu-id="31248-268">Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli w celu ustalenia, czy wszystkie podsystemy i zasoby aplikacji są dostępne.</span><span class="sxs-lookup"><span data-stu-id="31248-268">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="31248-269">Sprawdzenie na żywo powoduje jedynie szybkie sprawdzenie, czy aplikacja jest dostępna do przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="31248-269">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="31248-270">Gdy aplikacja przejdzie kontrolę gotowości, nie ma potrzeby dalszej obciążania aplikacji przy użyciu kosztownego zestawu kontroli&mdash;gotowości sprawdza tylko, czy sprawdzanie dostępności jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="31248-270">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="31248-271">Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="31248-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="31248-272">`StartupHostedServiceHealthCheck` Uwidacznia właściwość, `StartupTaskCompleted`która może zostać `true` ustawiona przez usługę hostowaną po zakończeniu długotrwałego zadania (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="31248-273">Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) (*usługi/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="31248-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="31248-274">Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony na `true`:</span><span class="sxs-lookup"><span data-stu-id="31248-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="31248-275">Kontrola kondycji jest zarejestrowana <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w `Startup.ConfigureServices` programie w programie wraz z usługą hostowaną.</span><span class="sxs-lookup"><span data-stu-id="31248-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="31248-276">Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="31248-277">Punkt końcowy sprawdzania kondycji jest tworzony przez `MapHealthChecks` wywołanie `Startup.Configure`w.</span><span class="sxs-lookup"><span data-stu-id="31248-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="31248-278">W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone w:</span><span class="sxs-lookup"><span data-stu-id="31248-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="31248-279">`/health/ready`w celu sprawdzenia gotowości.</span><span class="sxs-lookup"><span data-stu-id="31248-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="31248-280">Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji `ready` za pomocą znacznika.</span><span class="sxs-lookup"><span data-stu-id="31248-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="31248-281">`/health/live`na potrzeby kontroli na żywo.</span><span class="sxs-lookup"><span data-stu-id="31248-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="31248-282">Sprawdzenie stanu na żywo odfiltruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` elementu [HealthCheckOptions. predykatu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, zobacz [Sprawdzanie kondycji filtru](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="31248-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="31248-283">W poniższym przykładowym kodzie:</span><span class="sxs-lookup"><span data-stu-id="31248-283">In the following example code:</span></span>

* <span data-ttu-id="31248-284">Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".</span><span class="sxs-lookup"><span data-stu-id="31248-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="31248-285">`Predicate` Wyklucza wszystkie testy i zwracają 200-OK.</span><span class="sxs-lookup"><span data-stu-id="31248-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="31248-286">Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="31248-287">W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="31248-288">Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="31248-289">Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.</span><span class="sxs-lookup"><span data-stu-id="31248-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="31248-290">W tym przykładzie tworzony jest również Wydawca sprawdzania kondycji (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacja), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="31248-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="31248-291">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="31248-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="31248-292">Przykład Kubernetes</span><span class="sxs-lookup"><span data-stu-id="31248-292">Kubernetes example</span></span>

<span data-ttu-id="31248-293">Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="31248-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="31248-294">W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="31248-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="31248-295">Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="31248-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="31248-296">Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="31248-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="31248-297">Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="31248-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="31248-298">Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="31248-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="31248-299">Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="31248-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="31248-300">`MemoryHealthCheck`zgłasza stan obniżonej wydajności, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji).</span><span class="sxs-lookup"><span data-stu-id="31248-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="31248-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Zawiera informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="31248-302">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-303">Zamiast włączać kontrolę kondycji przez przekazanie jej <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>do programu `MemoryHealthCheck` jest zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="31248-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="31248-304">Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="31248-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="31248-305">Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="31248-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="31248-306">W *CustomWriterStartup.cs* przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="31248-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="31248-307">Punkt końcowy sprawdzania kondycji jest tworzony przez `MapHealthChecks` wywołanie `Startup.Configure`w.</span><span class="sxs-lookup"><span data-stu-id="31248-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="31248-308">`WriteResponse` Delegat jest dostarczany do właściwości <Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions. ResponseWriter> do wyprowadzania NIESTANDARDOWEJ odpowiedzi JSON, gdy jest wykonywane sprawdzanie kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="31248-309">`WriteResponse` Delegat formatuje obiekt `CompositeHealthCheckResult` w formacie JSON i zwraca dane wyjściowe JSON dla odpowiedzi kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="31248-310">Aby uzyskać więcej informacji, zobacz sekcję [Dostosowywanie danych wyjściowych](#customize-output) .</span><span class="sxs-lookup"><span data-stu-id="31248-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="31248-311">Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="31248-312">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.</span><span class="sxs-lookup"><span data-stu-id="31248-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="31248-313">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="31248-314">Filtruj według portu</span><span class="sxs-lookup"><span data-stu-id="31248-314">Filter by port</span></span>

<span data-ttu-id="31248-315">`MapHealthChecks` Zadzwoń `RequireHost` do wzorca adresu URL, który określa port, aby ograniczyć żądania sprawdzania kondycji do określonego portu.</span><span class="sxs-lookup"><span data-stu-id="31248-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="31248-316">Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.</span><span class="sxs-lookup"><span data-stu-id="31248-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="31248-317">Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="31248-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="31248-318">Port jest ustawiany w pliku *profilu launchsettings. JSON* i przekazywać do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="31248-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="31248-319">Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.</span><span class="sxs-lookup"><span data-stu-id="31248-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="31248-320">Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz plik *profilu launchsettings. JSON* w folderze *Właściwości* .</span><span class="sxs-lookup"><span data-stu-id="31248-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="31248-321">Następujące *właściwości/profilu launchsettings. JSON* w przykładowej aplikacji nie znajdują się w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:</span><span class="sxs-lookup"><span data-stu-id="31248-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="31248-322">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-323">Utwórz punkt końcowy sprawdzania kondycji, `MapHealthChecks` wywołując `Startup.Configure`w.</span><span class="sxs-lookup"><span data-stu-id="31248-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="31248-324">W przykładowej aplikacji wywołanie do `RequireHost` punktu końcowego w programie `Startup.Configure` określa port zarządzania z konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="31248-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="31248-325">Punkty końcowe są tworzone w aplikacji przykładowej `Startup.Configure`w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="31248-326">W poniższym przykładowym kodzie:</span><span class="sxs-lookup"><span data-stu-id="31248-326">In the following example code:</span></span>

* <span data-ttu-id="31248-327">Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".</span><span class="sxs-lookup"><span data-stu-id="31248-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="31248-328">`Predicate` Wyklucza wszystkie testy i zwracają 200-OK.</span><span class="sxs-lookup"><span data-stu-id="31248-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="31248-329">Można uniknąć tworzenia pliku *profilu launchsettings. JSON* w przykładowej aplikacji przez ustawienie portu zarządzania jawnie w kodzie.</span><span class="sxs-lookup"><span data-stu-id="31248-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="31248-330">W *program.cs* , gdzie <xref:Microsoft.Extensions.Hosting.HostBuilder> został utworzony, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> i Podaj punkt końcowy portu zarządzania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="31248-331">W `Configure` programie *ManagementPortStartup.cs*określ port zarządzania przy użyciu `RequireHost`:</span><span class="sxs-lookup"><span data-stu-id="31248-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="31248-332">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="31248-332">*Program.cs*:</span></span>
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
> <span data-ttu-id="31248-333">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="31248-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="31248-334">Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="31248-335">Dystrybucja biblioteki kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-335">Distribute a health check library</span></span>

<span data-ttu-id="31248-336">Aby rozpowszechnić kontrolę kondycji jako bibliotekę:</span><span class="sxs-lookup"><span data-stu-id="31248-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="31248-337">Napisz kontrolę kondycji, która implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejs jako autonomiczną klasę.</span><span class="sxs-lookup"><span data-stu-id="31248-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="31248-338">Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="31248-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="31248-339">W logice kontroli kondycji `CheckHealthAsync`:</span><span class="sxs-lookup"><span data-stu-id="31248-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="31248-340">`data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.</span><span class="sxs-lookup"><span data-stu-id="31248-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="31248-341">`AccessViolationException`jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="31248-342"><xref:System.AccessViolationException> Gdy wystąpi, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostaje zwrócony z, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="31248-343">Napisz metodę rozszerzenia z parametrami, które są używane przez zużywaną aplikację `Startup.Configure` w jej metodzie.</span><span class="sxs-lookup"><span data-stu-id="31248-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="31248-344">W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="31248-345">Poprzednia sygnatura wskazuje `ExampleHealthCheck` , że wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="31248-346">Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="31248-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="31248-347">W poniższym przykładzie obiekt wywołujący określa opcjonalne:</span><span class="sxs-lookup"><span data-stu-id="31248-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="31248-348">Nazwa sprawdzania kondycji`name`().</span><span class="sxs-lookup"><span data-stu-id="31248-348">health check name (`name`).</span></span> <span data-ttu-id="31248-349">Jeśli `null` `example_health_check` jest używany.</span><span class="sxs-lookup"><span data-stu-id="31248-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="31248-350">punkt danych ciągu dla kontroli kondycji (`data1`).</span><span class="sxs-lookup"><span data-stu-id="31248-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="31248-351">punkt danych liczb całkowitych dla kontroli kondycji (`data2`).</span><span class="sxs-lookup"><span data-stu-id="31248-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="31248-352">Jeśli `null` `1` jest używany.</span><span class="sxs-lookup"><span data-stu-id="31248-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="31248-353">stan niepowodzenia<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>().</span><span class="sxs-lookup"><span data-stu-id="31248-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="31248-354">Wartość domyślna to `null`.</span><span class="sxs-lookup"><span data-stu-id="31248-354">The default is `null`.</span></span> <span data-ttu-id="31248-355">Jeśli `null`dla stanu błędu zostanie zgłoszona wartość [HealthStatus. w złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="31248-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="31248-356">Tagi (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="31248-356">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="31248-357">Wydawca kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-357">Health Check Publisher</span></span>

<span data-ttu-id="31248-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Po dodaniu do kontenera usługi system kontroli kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` wynik.</span><span class="sxs-lookup"><span data-stu-id="31248-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="31248-359">Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="31248-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Interfejs ma jedną metodę:</span><span class="sxs-lookup"><span data-stu-id="31248-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="31248-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>Zezwól na ustawienie:</span><span class="sxs-lookup"><span data-stu-id="31248-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="31248-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Początkowe opóźnienie stosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="31248-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="31248-363">Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji.</span><span class="sxs-lookup"><span data-stu-id="31248-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="31248-364">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-364">The default value is five seconds.</span></span>
* <span data-ttu-id="31248-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wykonania.</span><span class="sxs-lookup"><span data-stu-id="31248-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="31248-366">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="31248-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane testy kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="31248-368">Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli.</span><span class="sxs-lookup"><span data-stu-id="31248-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="31248-369">Predykat jest oceniany w każdym okresie.</span><span class="sxs-lookup"><span data-stu-id="31248-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="31248-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Limit czasu wykonywania kontroli kondycji dla wszystkich <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="31248-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="31248-371">Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan> , aby wykonać bez limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="31248-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="31248-372">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="31248-373">W przykładowej aplikacji `ReadinessPublisher` jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacją.</span><span class="sxs-lookup"><span data-stu-id="31248-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="31248-374">Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia na poziomie dziennika:</span><span class="sxs-lookup"><span data-stu-id="31248-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="31248-375">Informacja (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), jeśli stan kontroli kondycji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>to.</span><span class="sxs-lookup"><span data-stu-id="31248-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="31248-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span><span class="sxs-lookup"><span data-stu-id="31248-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="31248-377">W `LivenessProbeStartup` przykładzie przykładowej aplikacji sprawdzanie `StartupHostedService` gotowości ma dwa drugie opóźnienie uruchamiania i sprawdza, co 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="31248-378">Aby uaktywnić <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, przykład rejestruje `ReadinessPublisher` się jako usługa pojedyncza w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="31248-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="31248-379">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="31248-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="31248-380">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="31248-381">Ogranicz kontrolę kondycji za pomocą MapWhen</span><span class="sxs-lookup"><span data-stu-id="31248-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="31248-382">Użyj <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> , aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="31248-383">W poniższym przykładzie `MapWhen` rozgałęzienie potoku żądania w celu aktywowania kontroli kondycji oprogramowania pośredniczącego w przypadku otrzymania żądania `api/HealthCheck` Get dla punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="31248-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="31248-384">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="31248-384">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="31248-385">ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="31248-386">Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP.</span><span class="sxs-lookup"><span data-stu-id="31248-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="31248-387">Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:</span><span class="sxs-lookup"><span data-stu-id="31248-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="31248-388">Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="31248-389">Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera.</span><span class="sxs-lookup"><span data-stu-id="31248-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="31248-390">Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="31248-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="31248-391">Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.</span><span class="sxs-lookup"><span data-stu-id="31248-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="31248-392">Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.</span><span class="sxs-lookup"><span data-stu-id="31248-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="31248-393">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="31248-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="31248-394">Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="31248-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="31248-395">Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="31248-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="31248-396">Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31248-397">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="31248-397">Prerequisites</span></span>

<span data-ttu-id="31248-398">Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="31248-399">Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany.</span><span class="sxs-lookup"><span data-stu-id="31248-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="31248-400">System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.</span><span class="sxs-lookup"><span data-stu-id="31248-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="31248-401">Odwołującego się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu w pakiecie [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .</span><span class="sxs-lookup"><span data-stu-id="31248-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="31248-402">Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="31248-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="31248-403">Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="31248-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="31248-404">Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="31248-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="31248-405">Aby poznać scenariusze baz danych, przykładową aplikację:</span><span class="sxs-lookup"><span data-stu-id="31248-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="31248-406">Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="31248-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="31248-407">W pliku projektu znajdują się następujące odwołania do pakietów:</span><span class="sxs-lookup"><span data-stu-id="31248-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="31248-408">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="31248-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="31248-409">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="31248-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="31248-410">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="31248-411">W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="31248-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="31248-412">Przykładowa aplikacja wymaga utworzenia pliku *Properties/profilu launchsettings. JSON* , który zawiera adres URL zarządzania i port zarządzania.</span><span class="sxs-lookup"><span data-stu-id="31248-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="31248-413">Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="31248-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="31248-414">Podstawowa sonda kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-414">Basic health probe</span></span>

<span data-ttu-id="31248-415">W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania,*liveness*jest wystarczająca do odnajdywania stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="31248-416">Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję.</span><span class="sxs-lookup"><span data-stu-id="31248-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="31248-417">Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu.</span><span class="sxs-lookup"><span data-stu-id="31248-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="31248-418">Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="31248-419">Domyślny moduł zapisujący odpowiedzi zapisuje stan (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżone](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus stan złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="31248-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="31248-420">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-421">Dodaj punkt końcowy dla programu testowego kondycji <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> programu z programu w potoku `Startup.Configure`przetwarzania żądania.</span><span class="sxs-lookup"><span data-stu-id="31248-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="31248-422">W przykładowej aplikacji jest tworzony punkt końcowy sprawdzania kondycji `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="31248-423">Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="31248-424">Przykład platformy Docker</span><span class="sxs-lookup"><span data-stu-id="31248-424">Docker example</span></span>

<span data-ttu-id="31248-425">Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="31248-426">Utwórz kontrole kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-426">Create health checks</span></span>

<span data-ttu-id="31248-427">Kontrole kondycji są tworzone przez implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="31248-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="31248-428"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Metoda zwraca <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> wartość wskazującą kondycję jako `Healthy`, `Degraded`lub. `Unhealthy`</span><span class="sxs-lookup"><span data-stu-id="31248-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="31248-429">Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="31248-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="31248-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwracać opcjonalne pary klucz-wartość.</span><span class="sxs-lookup"><span data-stu-id="31248-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="31248-431">Przykładowe Sprawdzenie kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-431">Example health check</span></span>

<span data-ttu-id="31248-432">W poniższej `ExampleHealthCheck` klasie przedstawiono układ kontroli kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="31248-433">Logika kontroli kondycji jest umieszczana `CheckHealthAsync` w metodzie.</span><span class="sxs-lookup"><span data-stu-id="31248-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="31248-434">Poniższy przykład ustawia zmienną `healthCheckResultHealthy`fikcyjną, do. `true`</span><span class="sxs-lookup"><span data-stu-id="31248-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="31248-435">Jeśli wartość `healthCheckResultHealthy` jest równa `false`, zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .</span><span class="sxs-lookup"><span data-stu-id="31248-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="31248-436">Rejestrowanie usług sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-436">Register health check services</span></span>

<span data-ttu-id="31248-437">`ExampleHealthCheck` Typ jest dodawany do usług sprawdzania kondycji w `Startup.ConfigureServices` programie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>w programie:</span><span class="sxs-lookup"><span data-stu-id="31248-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="31248-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> Przeciążenie pokazane w poniższym przykładzie ustawia stan błędu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) w celu raportowania, kiedy Sprawdzanie kondycji zgłasza błąd.</span><span class="sxs-lookup"><span data-stu-id="31248-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="31248-439">Jeśli stan niepowodzenia jest ustawiony na `null` wartość (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="31248-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="31248-440">To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.</span><span class="sxs-lookup"><span data-stu-id="31248-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="31248-441">*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="31248-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="31248-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda.</span><span class="sxs-lookup"><span data-stu-id="31248-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="31248-443">W poniższym `Startup.ConfigureServices` przykładzie nazwa sprawdzania kondycji jest określona jako `Example` , a sprawdzanie zawsze zwraca prawidłowy stan:</span><span class="sxs-lookup"><span data-stu-id="31248-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="31248-444">Używanie oprogramowania do sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="31248-445">W `Startup.Configure`programie Wywołaj <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> w potoku przetwarzania z adresem URL punktu końcowego lub ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="31248-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="31248-446">Jeśli kontrole kondycji powinny nasłuchiwać określonego portu, Użyj przeciążenia, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> aby ustawić port (dokładniej opisane w sekcji [Filtruj według portu](#filter-by-port) ):</span><span class="sxs-lookup"><span data-stu-id="31248-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="31248-447">Opcje sprawdzania kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-447">Health check options</span></span>

<span data-ttu-id="31248-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="31248-449">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="31248-450">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="31248-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="31248-451">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="31248-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="31248-452">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="31248-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="31248-453">Filtrowanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-453">Filter health checks</span></span>

<span data-ttu-id="31248-454">Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="31248-455">Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną dla <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji.</span><span class="sxs-lookup"><span data-stu-id="31248-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="31248-456">W `Bar` poniższym przykładzie Sprawdzanie kondycji jest odfiltrowane przez tag (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> Właściwość sprawdzania kondycji jest zgodna `foo_tag` lub: `baz_tag`</span><span class="sxs-lookup"><span data-stu-id="31248-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="31248-457">Dostosowywanie kodu stanu HTTP</span><span class="sxs-lookup"><span data-stu-id="31248-457">Customize the HTTP status code</span></span>

<span data-ttu-id="31248-458">Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> , aby dostosować mapowanie stanu kondycji do kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="31248-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="31248-459">Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="31248-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="31248-460">Zmień wartości kodów stanu, aby spełniały Twoje wymagania.</span><span class="sxs-lookup"><span data-stu-id="31248-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="31248-461">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="31248-461">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="31248-462">Pomiń nagłówki pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="31248-462">Suppress cache headers</span></span>

<span data-ttu-id="31248-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Określa, czy kontrole kondycji powodują dodanie nagłówków HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="31248-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="31248-464">Jeśli `false` wartość jest (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje `Cache-Control`nagłówki, `Expires`i `Pragma` , aby zapobiec buforowaniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="31248-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="31248-465">Jeśli wartość to `true`, oprogramowanie pośredniczące nie modyfikuje nagłówków pamięci podręcznej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="31248-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="31248-466">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="31248-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="31248-467">Dostosuj dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="31248-467">Customize output</span></span>

<span data-ttu-id="31248-468"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Opcja Pobiera lub ustawia delegata używany do zapisywania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="31248-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="31248-469">Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="31248-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="31248-470">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="31248-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="31248-471">Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="31248-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="31248-472">Następujący delegat niestandardowy, `WriteResponse`, wyprowadza niestandardową odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="31248-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="31248-473">System kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="31248-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="31248-474">Możesz dowolnie dostosowywać `JObject` w powyższym przykładzie, aby zaspokoić Twoje potrzeby.</span><span class="sxs-lookup"><span data-stu-id="31248-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="31248-475">Sonda bazy danych</span><span class="sxs-lookup"><span data-stu-id="31248-475">Database probe</span></span>

<span data-ttu-id="31248-476">Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="31248-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="31248-477">Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych.</span><span class="sxs-lookup"><span data-stu-id="31248-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="31248-478">`AspNetCore.Diagnostics.HealthChecks`wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="31248-479">Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko.</span><span class="sxs-lookup"><span data-stu-id="31248-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="31248-480">Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności.</span><span class="sxs-lookup"><span data-stu-id="31248-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="31248-481">W większości przypadków uruchomienie zapytania testowego nie jest konieczne.</span><span class="sxs-lookup"><span data-stu-id="31248-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="31248-482">Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="31248-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="31248-483">Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="31248-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="31248-484">Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="31248-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="31248-485">Podaj prawidłowe parametry połączenia z bazą danych w pliku *appSettings. JSON* przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="31248-486">Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="31248-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="31248-487">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-488">Przykładowa aplikacja wywołuje `AddSqlServer` metodę z parametrami połączenia bazy danych (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="31248-489">Wywołaj kontrolę kondycji oprogramowania pośredniczącego w potoku `Startup.Configure`przetwarzania aplikacji w:</span><span class="sxs-lookup"><span data-stu-id="31248-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="31248-490">Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="31248-491">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="31248-492">Badanie Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="31248-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="31248-493">`DbContext` Sprawdzanie potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="31248-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="31248-494">`DbContext` Sprawdzanie jest obsługiwane w aplikacjach, które:</span><span class="sxs-lookup"><span data-stu-id="31248-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="31248-495">Użyj [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="31248-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="31248-496">Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="31248-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="31248-497">`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla elementu `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="31248-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="31248-498">`DbContext` Jest dostarczany jako `TContext` do metody.</span><span class="sxs-lookup"><span data-stu-id="31248-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="31248-499">Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.</span><span class="sxs-lookup"><span data-stu-id="31248-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="31248-500">Domyślnie:</span><span class="sxs-lookup"><span data-stu-id="31248-500">By default:</span></span>

* <span data-ttu-id="31248-501">`CanConnectAsync` Metoda `DbContextHealthCheck` wywołań EF Core.</span><span class="sxs-lookup"><span data-stu-id="31248-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="31248-502">Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metod.</span><span class="sxs-lookup"><span data-stu-id="31248-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="31248-503">Nazwa sprawdzania kondycji jest nazwą `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="31248-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="31248-504">`AppDbContext` W przykładowej aplikacji jest `AddDbContextCheck` dostarczany i zarejestrowany jako usługa w `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="31248-505">W przykładowej aplikacji program `UseHealthChecks` dodaje do programu oprogramowanie do sprawdzania kondycji `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="31248-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="31248-506">Aby uruchomić scenariusz `DbContext` sondowania za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="31248-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="31248-507">Jeśli baza danych istnieje, usuń ją.</span><span class="sxs-lookup"><span data-stu-id="31248-507">If the database exists, delete it.</span></span>

<span data-ttu-id="31248-508">Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="31248-509">Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do `/health` punktu końcowego w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="31248-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="31248-510">Baza danych programu `AppDbContext` i nie istnieje, więc aplikacja udostępnia następujące odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="31248-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="31248-511">Wyzwól przykładową aplikację, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="31248-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="31248-512">Wprowadź żądanie do `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="31248-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="31248-513">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="31248-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="31248-514">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="31248-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="31248-515">Istnieje baza danych i kontekst, aby aplikacja odpowiadała:</span><span class="sxs-lookup"><span data-stu-id="31248-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="31248-516">Wyzwól przykładową aplikację, aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="31248-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="31248-517">Wprowadź żądanie do `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="31248-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="31248-518">Aplikacja odpowiada:</span><span class="sxs-lookup"><span data-stu-id="31248-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="31248-519">Utwórz żądanie do `/health` punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="31248-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="31248-520">Aplikacja zawiera odpowiedź w złej kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="31248-521">Oddzielne sondy gotowości i na żywo</span><span class="sxs-lookup"><span data-stu-id="31248-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="31248-522">W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:</span><span class="sxs-lookup"><span data-stu-id="31248-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="31248-523">Aplikacja działa, ale jeszcze nie jest gotowa do odbierania żądań.</span><span class="sxs-lookup"><span data-stu-id="31248-523">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="31248-524">Jest to stan *gotowości*aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-524">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="31248-525">Aplikacja działa i odpowiada na żądania.</span><span class="sxs-lookup"><span data-stu-id="31248-525">The app is functioning and responding to requests.</span></span> <span data-ttu-id="31248-526">Ten stan jest *aktywny*.</span><span class="sxs-lookup"><span data-stu-id="31248-526">This state is the app's *liveness*.</span></span>

<span data-ttu-id="31248-527">Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli w celu ustalenia, czy wszystkie podsystemy i zasoby aplikacji są dostępne.</span><span class="sxs-lookup"><span data-stu-id="31248-527">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="31248-528">Sprawdzenie na żywo powoduje jedynie szybkie sprawdzenie, czy aplikacja jest dostępna do przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="31248-528">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="31248-529">Gdy aplikacja przejdzie kontrolę gotowości, nie ma potrzeby dalszej obciążania aplikacji przy użyciu kosztownego zestawu kontroli&mdash;gotowości sprawdza tylko, czy sprawdzanie dostępności jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="31248-529">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="31248-530">Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="31248-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="31248-531">`StartupHostedServiceHealthCheck` Uwidacznia właściwość, `StartupTaskCompleted`która może zostać `true` ustawiona przez usługę hostowaną po zakończeniu długotrwałego zadania (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="31248-532">Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) (*usługi/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="31248-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="31248-533">Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony na `true`:</span><span class="sxs-lookup"><span data-stu-id="31248-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="31248-534">Kontrola kondycji jest zarejestrowana <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w `Startup.ConfigureServices` programie w programie wraz z usługą hostowaną.</span><span class="sxs-lookup"><span data-stu-id="31248-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="31248-535">Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="31248-536">Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku `Startup.Configure`przetwarzania aplikacji w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="31248-537">W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone na `/health/ready` potrzeby kontroli gotowości i `/health/live` kontroli stanu na żywo.</span><span class="sxs-lookup"><span data-stu-id="31248-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="31248-538">Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji `ready` za pomocą znacznika.</span><span class="sxs-lookup"><span data-stu-id="31248-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="31248-539">Sprawdzenie stanu na żywo odfiltruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` elementu [HealthCheckOptions. predykatu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, zobacz [Sprawdzanie kondycji filtru](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="31248-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="31248-540">Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="31248-541">W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="31248-542">Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="31248-543">Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.</span><span class="sxs-lookup"><span data-stu-id="31248-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="31248-544">W tym przykładzie tworzony jest również Wydawca sprawdzania kondycji (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacja), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="31248-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="31248-545">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="31248-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="31248-546">Przykład Kubernetes</span><span class="sxs-lookup"><span data-stu-id="31248-546">Kubernetes example</span></span>

<span data-ttu-id="31248-547">Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="31248-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="31248-548">W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="31248-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="31248-549">Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="31248-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="31248-550">Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="31248-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="31248-551">Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="31248-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="31248-552">Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="31248-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="31248-553">Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="31248-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="31248-554">`MemoryHealthCheck`zgłasza stan złej kondycji, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji).</span><span class="sxs-lookup"><span data-stu-id="31248-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="31248-555"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Zawiera informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="31248-556">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-557">Zamiast włączać kontrolę kondycji przez przekazanie jej <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>do programu `MemoryHealthCheck` jest zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="31248-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="31248-558">Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="31248-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="31248-559">Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="31248-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="31248-560">W przykładowej aplikacji (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="31248-561">Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku `Startup.Configure`przetwarzania aplikacji w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="31248-562">`WriteResponse` Obiekt delegowany jest dostarczany do `ResponseWriter` właściwości w celu wygenerowania niestandardowej odpowiedzi JSON po zakończeniu sprawdzania kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="31248-563">`WriteResponse` Metoda formatuje `CompositeHealthCheckResult` do obiektu JSON i zwraca dane wyjściowe JSON dla odpowiedzi kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="31248-564">Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="31248-565">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.</span><span class="sxs-lookup"><span data-stu-id="31248-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="31248-566">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="31248-567">Filtruj według portu</span><span class="sxs-lookup"><span data-stu-id="31248-567">Filter by port</span></span>

<span data-ttu-id="31248-568">Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> z portem ogranicza liczbę żądań sprawdzania kondycji do określonego portu.</span><span class="sxs-lookup"><span data-stu-id="31248-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="31248-569">Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.</span><span class="sxs-lookup"><span data-stu-id="31248-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="31248-570">Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="31248-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="31248-571">Port jest ustawiany w pliku *profilu launchsettings. JSON* i przekazywać do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="31248-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="31248-572">Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.</span><span class="sxs-lookup"><span data-stu-id="31248-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="31248-573">Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz plik *profilu launchsettings. JSON* w folderze *Właściwości* .</span><span class="sxs-lookup"><span data-stu-id="31248-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="31248-574">Następujące *właściwości/profilu launchsettings. JSON* w przykładowej aplikacji nie znajdują się w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:</span><span class="sxs-lookup"><span data-stu-id="31248-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="31248-575">Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w `Startup.ConfigureServices`programie w programie.</span><span class="sxs-lookup"><span data-stu-id="31248-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31248-576">Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> określające port zarządzania (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="31248-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="31248-577">Możesz uniknąć tworzenia pliku *profilu launchsettings. JSON* w przykładowej aplikacji, ustawiając adresy URL i port zarządzania jawnie w kodzie.</span><span class="sxs-lookup"><span data-stu-id="31248-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="31248-578">W *program.cs* , w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> którym został utworzony, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> i podaj normalny punkt końcowy odpowiedzi aplikacji oraz punkt końcowy portu zarządzania.</span><span class="sxs-lookup"><span data-stu-id="31248-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="31248-579">W *ManagementPortStartup.cs* , <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> gdzie jest wywoływana, określ port zarządzania jawnie.</span><span class="sxs-lookup"><span data-stu-id="31248-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="31248-580">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="31248-580">*Program.cs*:</span></span>
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
> <span data-ttu-id="31248-581">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="31248-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="31248-582">Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="31248-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="31248-583">Dystrybucja biblioteki kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-583">Distribute a health check library</span></span>

<span data-ttu-id="31248-584">Aby rozpowszechnić kontrolę kondycji jako bibliotekę:</span><span class="sxs-lookup"><span data-stu-id="31248-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="31248-585">Napisz kontrolę kondycji, która implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejs jako autonomiczną klasę.</span><span class="sxs-lookup"><span data-stu-id="31248-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="31248-586">Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="31248-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="31248-587">W logice kontroli kondycji `CheckHealthAsync`:</span><span class="sxs-lookup"><span data-stu-id="31248-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="31248-588">`data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.</span><span class="sxs-lookup"><span data-stu-id="31248-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="31248-589">`AccessViolationException`jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="31248-590"><xref:System.AccessViolationException> Gdy wystąpi, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostaje zwrócony z, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="31248-591">Napisz metodę rozszerzenia z parametrami, które są używane przez zużywaną aplikację `Startup.Configure` w jej metodzie.</span><span class="sxs-lookup"><span data-stu-id="31248-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="31248-592">W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:</span><span class="sxs-lookup"><span data-stu-id="31248-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="31248-593">Poprzednia sygnatura wskazuje `ExampleHealthCheck` , że wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="31248-594">Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="31248-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="31248-595">W poniższym przykładzie obiekt wywołujący określa opcjonalne:</span><span class="sxs-lookup"><span data-stu-id="31248-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="31248-596">Nazwa sprawdzania kondycji`name`().</span><span class="sxs-lookup"><span data-stu-id="31248-596">health check name (`name`).</span></span> <span data-ttu-id="31248-597">Jeśli `null` `example_health_check` jest używany.</span><span class="sxs-lookup"><span data-stu-id="31248-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="31248-598">punkt danych ciągu dla kontroli kondycji (`data1`).</span><span class="sxs-lookup"><span data-stu-id="31248-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="31248-599">punkt danych liczb całkowitych dla kontroli kondycji (`data2`).</span><span class="sxs-lookup"><span data-stu-id="31248-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="31248-600">Jeśli `null` `1` jest używany.</span><span class="sxs-lookup"><span data-stu-id="31248-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="31248-601">stan niepowodzenia<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>().</span><span class="sxs-lookup"><span data-stu-id="31248-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="31248-602">Wartość domyślna to `null`.</span><span class="sxs-lookup"><span data-stu-id="31248-602">The default is `null`.</span></span> <span data-ttu-id="31248-603">Jeśli `null`dla stanu błędu zostanie zgłoszona wartość [HealthStatus. w złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="31248-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="31248-604">Tagi (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="31248-604">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="31248-605">Wydawca kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="31248-605">Health Check Publisher</span></span>

<span data-ttu-id="31248-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Po dodaniu do kontenera usługi system kontroli kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` wynik.</span><span class="sxs-lookup"><span data-stu-id="31248-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="31248-607">Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="31248-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Interfejs ma jedną metodę:</span><span class="sxs-lookup"><span data-stu-id="31248-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="31248-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>Zezwól na ustawienie:</span><span class="sxs-lookup"><span data-stu-id="31248-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="31248-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Początkowe opóźnienie stosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="31248-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="31248-611">Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji.</span><span class="sxs-lookup"><span data-stu-id="31248-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="31248-612">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-612">The default value is five seconds.</span></span>
* <span data-ttu-id="31248-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wykonania.</span><span class="sxs-lookup"><span data-stu-id="31248-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="31248-614">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="31248-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane testy kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="31248-616">Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli.</span><span class="sxs-lookup"><span data-stu-id="31248-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="31248-617">Predykat jest oceniany w każdym okresie.</span><span class="sxs-lookup"><span data-stu-id="31248-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="31248-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Limit czasu wykonywania kontroli kondycji dla wszystkich <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="31248-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="31248-619">Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan> , aby wykonać bez limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="31248-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="31248-620">Wartość domyślna to 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="31248-621">W wersji 2,2 ASP.NET Core ustawienie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nie jest honorowane przez <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację; ustawia wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="31248-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="31248-622">Ten problem został rozwiązany w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="31248-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="31248-623">W przykładowej aplikacji `ReadinessPublisher` jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacją.</span><span class="sxs-lookup"><span data-stu-id="31248-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="31248-624">Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia jako:</span><span class="sxs-lookup"><span data-stu-id="31248-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="31248-625">Informacja (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), jeśli stan kontroli kondycji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>to.</span><span class="sxs-lookup"><span data-stu-id="31248-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="31248-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span><span class="sxs-lookup"><span data-stu-id="31248-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="31248-627">W `LivenessProbeStartup` przykładzie przykładowej aplikacji sprawdzanie `StartupHostedService` gotowości ma dwa drugie opóźnienie uruchamiania i sprawdza, co 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="31248-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="31248-628">Aby uaktywnić <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, przykład rejestruje `ReadinessPublisher` się jako usługa pojedyncza w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="31248-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="31248-629">Następujące obejście umożliwia dodanie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpienia do kontenera usługi, gdy co najmniej jedna inna usługa hostowana została już dodana do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="31248-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="31248-630">To obejście nie będzie wymagane w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="31248-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="31248-631">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="31248-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="31248-632">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="31248-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="31248-633">Ogranicz kontrolę kondycji za pomocą MapWhen</span><span class="sxs-lookup"><span data-stu-id="31248-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="31248-634">Użyj <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> , aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.</span><span class="sxs-lookup"><span data-stu-id="31248-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="31248-635">W poniższym przykładzie `MapWhen` rozgałęzienie potoku żądania w celu aktywowania kontroli kondycji oprogramowania pośredniczącego w przypadku otrzymania żądania `api/HealthCheck` Get dla punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="31248-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="31248-636">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="31248-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
