---
<span data-ttu-id="713b4-101">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-103">'Blazor'</span></span>
- <span data-ttu-id="713b4-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-104">'Identity'</span></span>
- <span data-ttu-id="713b4-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-106">'Razor'</span></span>
- <span data-ttu-id="713b4-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="713b4-108">ASP.NET Core Blazor wstrzykiwania zależności</span><span class="sxs-lookup"><span data-stu-id="713b4-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="713b4-109">Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="713b4-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="713b4-110">obsługuje [iniekcję zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="713b4-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="713b4-111">Aplikacje mogą używać wbudowanych usług, wprowadzając je do składników programu.</span><span class="sxs-lookup"><span data-stu-id="713b4-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="713b4-112">Aplikacje mogą także definiować i rejestrować niestandardowe usługi i udostępniać je w całej aplikacji za pomocą funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="713b4-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="713b4-113">DI jest techniką uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="713b4-114">Może to być przydatne w Blazor aplikacjach, aby:</span><span class="sxs-lookup"><span data-stu-id="713b4-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="713b4-115">Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanej jako *pojedyncze usługi.*</span><span class="sxs-lookup"><span data-stu-id="713b4-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="713b4-116">Oddziel składniki od klas konkretnych usług za pomocą abstrakcji odwołań.</span><span class="sxs-lookup"><span data-stu-id="713b4-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="713b4-117">Rozważmy na przykład interfejs `IDataAccess` do uzyskiwania dostępu do danych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="713b4-118">Interfejs jest implementowany przez konkretną `DataAccess` klasę i zarejestrowany jako usługa w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="713b4-119">Gdy składnik używa elementu DI do odbierania `IDataAccess` implementacji, składnik nie jest połączony z konkretnym typem.</span><span class="sxs-lookup"><span data-stu-id="713b4-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="713b4-120">Implementacja może zostać zamieniony, być może dla implementacji makiety w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="713b4-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="713b4-121">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="713b4-121">Default services</span></span>

<span data-ttu-id="713b4-122">Domyślne usługi są automatycznie dodawane do kolekcji usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="713b4-123">Usługa</span><span class="sxs-lookup"><span data-stu-id="713b4-123">Service</span></span> | <span data-ttu-id="713b4-124">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="713b4-124">Lifetime</span></span> | <span data-ttu-id="713b4-125">Opis</span><span class="sxs-lookup"><span data-stu-id="713b4-125">Description</span></span> |
| ---
<span data-ttu-id="713b4-126">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-127">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-128">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-128">'Blazor'</span></span>
- <span data-ttu-id="713b4-129">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-129">'Identity'</span></span>
- <span data-ttu-id="713b4-130">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-131">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-131">'Razor'</span></span>
- <span data-ttu-id="713b4-132">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-132">'SignalR' uid:</span></span> 

<span data-ttu-id="713b4-133">---- | ---title: "ASP.NET Core Blazor wstrzykiwania zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-134">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-135">'Blazor'</span></span>
- <span data-ttu-id="713b4-136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-136">'Identity'</span></span>
- <span data-ttu-id="713b4-137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-138">'Razor'</span></span>
- <span data-ttu-id="713b4-139">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="713b4-140">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-141">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-142">'Blazor'</span></span>
- <span data-ttu-id="713b4-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-143">'Identity'</span></span>
- <span data-ttu-id="713b4-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-145">'Razor'</span></span>
- <span data-ttu-id="713b4-146">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-146">'SignalR' uid:</span></span> 

<span data-ttu-id="713b4-147">---- | ---title: "ASP.NET Core Blazor wstrzykiwania zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-148">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-149">'Blazor'</span></span>
- <span data-ttu-id="713b4-150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-150">'Identity'</span></span>
- <span data-ttu-id="713b4-151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-152">'Razor'</span></span>
- <span data-ttu-id="713b4-153">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="713b4-154">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-155">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-156">'Blazor'</span></span>
- <span data-ttu-id="713b4-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-157">'Identity'</span></span>
- <span data-ttu-id="713b4-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-159">'Razor'</span></span>
- <span data-ttu-id="713b4-160">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="713b4-161">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-162">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-163">'Blazor'</span></span>
- <span data-ttu-id="713b4-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-164">'Identity'</span></span>
- <span data-ttu-id="713b4-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-166">'Razor'</span></span>
- <span data-ttu-id="713b4-167">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-167">'SignalR' uid:</span></span> 

<span data-ttu-id="713b4-168">------ | | <xref:System.Net.Http.HttpClient> | Przejściowe | Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="713b4-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="713b4-169">Wystąpienie elementu `HttpClient` w aplikacji sieci Blazor webassembly używa przeglądarki do obsługi ruchu HTTP w tle.</span><span class="sxs-lookup"><span data-stu-id="713b4-169">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="713b4-170">Aplikacje serwera nie domyślnie zawierają `HttpClient` skonfigurowane jako usługa.</span><span class="sxs-lookup"><span data-stu-id="713b4-170"> Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="713b4-171">Udostępnianie `HttpClient` Blazor aplikacji serwerowej.</span><span class="sxs-lookup"><span data-stu-id="713b4-171">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="713b4-172">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="713b4-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="713b4-173">| | `IJSRuntime` | Pojedyncze ( Blazor zestaw webassembly)</span><span class="sxs-lookup"><span data-stu-id="713b4-173">| | `IJSRuntime` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="713b4-174">Zakres ( Blazor serwer) | Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="713b4-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="713b4-175">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="713b4-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="713b4-176">| | `NavigationManager` | Pojedyncze ( Blazor zestaw webassembly)</span><span class="sxs-lookup"><span data-stu-id="713b4-176">| | `NavigationManager` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="713b4-177">Zakres ( Blazor serwer) | Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="713b4-178">Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="713b4-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="713b4-179">Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli.</span><span class="sxs-lookup"><span data-stu-id="713b4-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="713b4-180">W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="713b4-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="713b4-181">Dodawanie usług do aplikacji</span><span class="sxs-lookup"><span data-stu-id="713b4-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="713b4-182">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="713b4-182"> WebAssembly</span></span>

<span data-ttu-id="713b4-183">Skonfiguruj usługi dla kolekcji usług aplikacji w `Main` metodzie *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="713b4-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="713b4-184">W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="713b4-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="713b4-185">Po skompilowaniu hosta usługi można uzyskać dostęp z poziomu głównego DI zakresu przed renderowaniem wszystkich składników.</span><span class="sxs-lookup"><span data-stu-id="713b4-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="713b4-186">Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:</span><span class="sxs-lookup"><span data-stu-id="713b4-186">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="713b4-187">Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="713b4-188">W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład *appSettings. JSON*) do `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="713b4-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Blazor<span data-ttu-id="713b4-189">Server</span><span class="sxs-lookup"><span data-stu-id="713b4-189"> Server</span></span>

<span data-ttu-id="713b4-190">Po utworzeniu nowej aplikacji zapoznaj się z tą `Startup.ConfigureServices` metodą:</span><span class="sxs-lookup"><span data-stu-id="713b4-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="713b4-191">`ConfigureServices`Metoda jest przenoszona <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a, która jest listą obiektów deskryptora usługi ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="713b4-191">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="713b4-192">Usługi są dodawane przez dostarczenie deskryptorów usługi do kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="713b4-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="713b4-193">Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="713b4-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="713b4-194">Okres istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="713b4-194">Service lifetime</span></span>

<span data-ttu-id="713b4-195">Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="713b4-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="713b4-196">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="713b4-196">Lifetime</span></span> | <span data-ttu-id="713b4-197">Opis</span><span class="sxs-lookup"><span data-stu-id="713b4-197">Description</span></span> |
| ---
<span data-ttu-id="713b4-198">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-199">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-200">'Blazor'</span></span>
- <span data-ttu-id="713b4-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-201">'Identity'</span></span>
- <span data-ttu-id="713b4-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-203">'Razor'</span></span>
- <span data-ttu-id="713b4-204">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="713b4-205">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-206">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-207">'Blazor'</span></span>
- <span data-ttu-id="713b4-208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-208">'Identity'</span></span>
- <span data-ttu-id="713b4-209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-210">'Razor'</span></span>
- <span data-ttu-id="713b4-211">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-211">'SignalR' uid:</span></span> 

<span data-ttu-id="713b4-212">---- | ---title: "ASP.NET Core Blazor wstrzykiwania zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-213">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-214">'Blazor'</span></span>
- <span data-ttu-id="713b4-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-215">'Identity'</span></span>
- <span data-ttu-id="713b4-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-217">'Razor'</span></span>
- <span data-ttu-id="713b4-218">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="713b4-219">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-220">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-221">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-221">'Blazor'</span></span>
- <span data-ttu-id="713b4-222">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-222">'Identity'</span></span>
- <span data-ttu-id="713b4-223">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-224">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-224">'Razor'</span></span>
- <span data-ttu-id="713b4-225">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="713b4-226">title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".</span><span class="sxs-lookup"><span data-stu-id="713b4-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="713b4-227">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="713b4-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="713b4-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="713b4-228">'Blazor'</span></span>
- <span data-ttu-id="713b4-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="713b4-229">'Identity'</span></span>
- <span data-ttu-id="713b4-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="713b4-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="713b4-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="713b4-231">'Razor'</span></span>
- <span data-ttu-id="713b4-232">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="713b4-232">'SignalR' uid:</span></span> 

<span data-ttu-id="713b4-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor Aplikacje webassembly nie mają obecnie koncepcji DI Scopes.</span><span class="sxs-lookup"><span data-stu-id="713b4-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="713b4-234">`Scoped`-zarejestrowane usługi zachowują się jak `Singleton` usługi.</span><span class="sxs-lookup"><span data-stu-id="713b4-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="713b4-235">Jednak Blazor model hostingu serwera obsługuje `Scoped` okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="713b4-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="713b4-236">W przypadku Blazor aplikacji serwerowych Rejestracja usługi w zakresie jest objęta zakresem *połączenia*.</span><span class="sxs-lookup"><span data-stu-id="713b4-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="713b4-237">Z tego powodu użycie usług objętych zakresem jest preferowane dla usług, które powinny być objęte zakresem bieżącego użytkownika, nawet jeśli bieżącym celem jest uruchomienie po stronie klienta w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="713b4-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="713b4-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI tworzy *pojedyncze wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="713b4-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="713b4-239">Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi.</span><span class="sxs-lookup"><span data-stu-id="713b4-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="713b4-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="713b4-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="713b4-241">System DI jest oparty na systemie DI w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="713b4-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="713b4-242">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="713b4-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="713b4-243">Żądanie usługi w składniku</span><span class="sxs-lookup"><span data-stu-id="713b4-243">Request a service in a component</span></span>

<span data-ttu-id="713b4-244">Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą dyrektywy [ \@ wstrzykiwania](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="713b4-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="713b4-245">`@inject`ma dwa parametry:</span><span class="sxs-lookup"><span data-stu-id="713b4-245">`@inject` has two parameters:</span></span>

* <span data-ttu-id="713b4-246">Wpisz &ndash; Typ usługi do dodania.</span><span class="sxs-lookup"><span data-stu-id="713b4-246">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="713b4-247">Właściwość &ndash; Nazwa właściwości otrzymującej wstrzykiwanej usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="713b4-247">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="713b4-248">Właściwość nie wymaga ręcznego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="713b4-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="713b4-249">Kompilator tworzy właściwość.</span><span class="sxs-lookup"><span data-stu-id="713b4-249">The compiler creates the property.</span></span>

<span data-ttu-id="713b4-250">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="713b4-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="713b4-251">Użyj wielu `@inject` instrukcji, aby wstrzyknąć różne usługi.</span><span class="sxs-lookup"><span data-stu-id="713b4-251">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="713b4-252">Poniższy przykład pokazuje, jak używać `@inject` .</span><span class="sxs-lookup"><span data-stu-id="713b4-252">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="713b4-253">Implementowanie usługi `Services.IDataAccess` jest wstrzykiwane do właściwości składnika `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="713b4-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="713b4-254">Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:</span><span class="sxs-lookup"><span data-stu-id="713b4-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="713b4-255">Wewnętrznie wygenerowana Właściwość ( `DataRepository` ) używa `InjectAttribute` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="713b4-255">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="713b4-256">Zazwyczaj ten atrybut nie jest używany bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="713b4-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="713b4-257">Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać `InjectAttribute` :</span><span class="sxs-lookup"><span data-stu-id="713b4-257">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="713b4-258">W składnikach pochodnych klasy bazowej `@inject` dyrektywa nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="713b4-258">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="713b4-259">`InjectAttribute`Klasa bazowa jest wystarczająca:</span><span class="sxs-lookup"><span data-stu-id="713b4-259">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="713b4-260">Korzystanie z usług DI w</span><span class="sxs-lookup"><span data-stu-id="713b4-260">Use DI in services</span></span>

<span data-ttu-id="713b4-261">Złożone usługi mogą wymagać dodatkowych usług.</span><span class="sxs-lookup"><span data-stu-id="713b4-261">Complex services might require additional services.</span></span> <span data-ttu-id="713b4-262">W poprzednim przykładzie `DataAccess` może być wymagana `HttpClient` Usługa domyślna.</span><span class="sxs-lookup"><span data-stu-id="713b4-262">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="713b4-263">`@inject`(lub `InjectAttribute` ) nie jest dostępny do użytku w usługach.</span><span class="sxs-lookup"><span data-stu-id="713b4-263">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="713b4-264">Zamiast tego należy użyć *iniekcji konstruktora* .</span><span class="sxs-lookup"><span data-stu-id="713b4-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="713b4-265">Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi.</span><span class="sxs-lookup"><span data-stu-id="713b4-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="713b4-266">Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="713b4-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="713b4-267">Wymagania wstępne dotyczące iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="713b4-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="713b4-268">Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI.</span><span class="sxs-lookup"><span data-stu-id="713b4-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="713b4-269">Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="713b4-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="713b4-270">Odpowiedni Konstruktor musi być *publiczny*.</span><span class="sxs-lookup"><span data-stu-id="713b4-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="713b4-271">Musi istnieć jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="713b4-271">One applicable constructor must exist.</span></span> <span data-ttu-id="713b4-272">W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="713b4-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="713b4-273">Klasy składników podstawowych narzędzi do zarządzania DI zakresem</span><span class="sxs-lookup"><span data-stu-id="713b4-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="713b4-274">W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="713b4-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="713b4-275">Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI.</span><span class="sxs-lookup"><span data-stu-id="713b4-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="713b4-276">W obszarze Blazor aplikacje serwera zakres żądań jest używany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="713b4-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="713b4-277">W Blazor aplikacjach webassembly usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="713b4-278">Zastosowanie tego typu w aplikacjach ogranicza okres istnienia usługi Blazor `OwningComponentBase` .</span><span class="sxs-lookup"><span data-stu-id="713b4-278">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="713b4-279">`OwningComponentBase`jest abstrakcyjnym typem pochodnym `ComponentBase` , który tworzy zakres di odpowiadający okresowi istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="713b4-279">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="713b4-280">Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika.</span><span class="sxs-lookup"><span data-stu-id="713b4-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="713b4-281">Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="713b4-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="713b4-282">Może to być przydatne w przypadku usług, które:</span><span class="sxs-lookup"><span data-stu-id="713b4-282">This can be useful for services that:</span></span>

* <span data-ttu-id="713b4-283">Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="713b4-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="713b4-284">Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="713b4-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="713b4-285">Dostępne są dwie wersje `OwningComponentBase` typu:</span><span class="sxs-lookup"><span data-stu-id="713b4-285">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="713b4-286">`OwningComponentBase`jest abstrakcyjnym, jednorazowym elementem podrzędnym `ComponentBase` typu z `ScopedServices` właściwością chronioną typu `IServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="713b4-286">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="713b4-287">Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="713b4-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="713b4-288">Usługi DI są wstrzykiwane do składnika przy użyciu `@inject` lub `InjectAttribute` ( `[Inject]` ) nie są tworzone w zakresie składnika.</span><span class="sxs-lookup"><span data-stu-id="713b4-288">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="713b4-289">Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane przy użyciu `ScopedServices.GetRequiredService` lub `ScopedServices.GetService` .</span><span class="sxs-lookup"><span data-stu-id="713b4-289">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="713b4-290">Wszystkie usługi rozpoznane przy użyciu `ScopedServices` dostawcy mają swoje zależności z tego samego zakresu.</span><span class="sxs-lookup"><span data-stu-id="713b4-290">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="713b4-291">`OwningComponentBase<T>`pochodzi z `OwningComponentBase` i dodaje właściwość `Service` zwracającą wystąpienie `T` z dostawcy i zakresu.</span><span class="sxs-lookup"><span data-stu-id="713b4-291">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="713b4-292">Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia, `IServiceProvider` gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika.</span><span class="sxs-lookup"><span data-stu-id="713b4-292">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="713b4-293">Ta `ScopedServices` Właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="713b4-293">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="713b4-294">Korzystanie z Entity Framework DbContext z elementu DI</span><span class="sxs-lookup"><span data-stu-id="713b4-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="713b4-295">Jednym z typowych typów usług pobieranych z aplikacji sieci Web typu "i" jest Entity Framework (EF) `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="713b4-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="713b4-296">Rejestrowanie usług EF przy użyciu `IServiceCollection.AddDbContext` `DbContext` Domyślnie dodaje usługę jako objętą zakresem.</span><span class="sxs-lookup"><span data-stu-id="713b4-296">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="713b4-297">Rejestracja w ramach usługi w zakresie może prowadzić do problemów w Blazor aplikacjach, ponieważ powoduje to `DbContext` , że wystąpienia są długotrwałe i udostępniane w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="713b4-297">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="713b4-298">`DbContext`nie jest bezpieczne dla wątków i nie może być używane współbieżnie.</span><span class="sxs-lookup"><span data-stu-id="713b4-298">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="713b4-299">W zależności od aplikacji korzystanie z programu `OwningComponentBase` w celu ograniczenia zakresu jednego `DbContext` składnika *może* rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="713b4-299">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="713b4-300">Jeśli składnik nie korzysta `DbContext` równolegle, wyprowadza składnik z `OwningComponentBase` i pobieranie `DbContext` z `ScopedServices` jest wystarczające, ponieważ gwarantuje to:</span><span class="sxs-lookup"><span data-stu-id="713b4-300">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="713b4-301">Oddzielne składniki nie współdzielą `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="713b4-301">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="713b4-302">`DbContext`Mieszka tylko tak długo, jak i w zależności od tego, czy jest to składnik.</span><span class="sxs-lookup"><span data-stu-id="713b4-302">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="713b4-303">Jeśli pojedynczy składnik może korzystać z współbieżnie `DbContext` (na przykład za każdym razem, gdy użytkownik wybierze przycisk), nawet przy użyciu `OwningComponentBase` nie należy unikać problemów z jednoczesnymi operacjami EF.</span><span class="sxs-lookup"><span data-stu-id="713b4-303">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="713b4-304">W takim przypadku należy użyć innej `DbContext` dla każdej operacji logicznej EF.</span><span class="sxs-lookup"><span data-stu-id="713b4-304">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="713b4-305">Użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="713b4-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="713b4-306">Utwórz `DbContext` bezpośrednio przy użyciu `DbContextOptions<TContext>` jako argument, który można pobrać z elementu "i" jest bezpieczny wątkowo.</span><span class="sxs-lookup"><span data-stu-id="713b4-306">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="713b4-307">Zarejestruj `DbContext` w kontenerze usługi z przejściowym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="713b4-307">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="713b4-308">Podczas rejestrowania kontekstu użyj `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="713b4-308">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="713b4-309">`AddDbContext`Metoda rozszerzenia przyjmuje dwa opcjonalne parametry typu `ServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="713b4-309">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="713b4-310">Aby skorzystać z tej metody, należy `contextLifetime` podać tylko parametr `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="713b4-310">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="713b4-311">`optionsLifetime`może zachować wartość domyślną `ServiceLifetime.Scoped` .</span><span class="sxs-lookup"><span data-stu-id="713b4-311">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="713b4-312">Przejściowy `DbContext` można wstrzyknąć jako normalny (przy użyciu `@inject` ) do składników, które nie wykonują równolegle wielu operacji EF.</span><span class="sxs-lookup"><span data-stu-id="713b4-312">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="713b4-313">Te, które mogą wykonywać wiele operacji EF jednocześnie, mogą zażądać oddzielnych `DbContext` obiektów dla każdej operacji równoległej przy użyciu `IServiceProvider.GetRequiredService` .</span><span class="sxs-lookup"><span data-stu-id="713b4-313">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="713b4-314">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="713b4-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
