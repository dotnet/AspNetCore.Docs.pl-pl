---
title: ASP.NET Iniekcja zależności rdzenia Blazor
author: guardrex
description: Zobacz, Blazor jak aplikacje mogą wstrzyknąć usługi do składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658075"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="8a981-103">ASP.NET rdzeń blazor uzależnienia iniekcji</span><span class="sxs-lookup"><span data-stu-id="8a981-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="8a981-104">Przez [Rainer Stropek](https://www.timecockpit.com) i [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="8a981-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8a981-105">Blazor obsługuje [wstrzykiwanie zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8a981-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8a981-106">Aplikacje mogą korzystać z wbudowanych usług, wstrzykując je do składników.</span><span class="sxs-lookup"><span data-stu-id="8a981-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="8a981-107">Aplikacje mogą również definiować i rejestrować usługi niestandardowe i udostępniać je w całej aplikacji za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="8a981-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="8a981-108">DI to technika uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="8a981-109">Może to być przydatne w aplikacjach Blazor do:</span><span class="sxs-lookup"><span data-stu-id="8a981-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="8a981-110">Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanych jako usługa *singleton.*</span><span class="sxs-lookup"><span data-stu-id="8a981-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="8a981-111">Odłącz komponenty od klas usług betonowych przy użyciu abstrakcji odwołań.</span><span class="sxs-lookup"><span data-stu-id="8a981-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="8a981-112">Rozważmy na przykład `IDataAccess` interfejs dostępu do danych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="8a981-113">Interfejs jest implementowany przez `DataAccess` klasę betonu i zarejestrowany jako usługa w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="8a981-114">Gdy składnik używa DI do `IDataAccess` odbierania implementacji, składnik nie jest sprzężona z typem betonu.</span><span class="sxs-lookup"><span data-stu-id="8a981-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="8a981-115">Implementacja może być zamieniona, być może dla implementacji makiety w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="8a981-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="8a981-116">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="8a981-116">Default services</span></span>

<span data-ttu-id="8a981-117">Usługi domyślne są automatycznie dodawane do kolekcji usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="8a981-118">Usługa</span><span class="sxs-lookup"><span data-stu-id="8a981-118">Service</span></span> | <span data-ttu-id="8a981-119">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="8a981-119">Lifetime</span></span> | <span data-ttu-id="8a981-120">Opis</span><span class="sxs-lookup"><span data-stu-id="8a981-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="8a981-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="8a981-121">Singleton</span></span> | <span data-ttu-id="8a981-122">Zawiera metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="8a981-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="8a981-123">Wystąpienie `HttpClient` w aplikacji Blazor WebAssembly używa przeglądarki do obsługi ruchu HTTP w tle.</span><span class="sxs-lookup"><span data-stu-id="8a981-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="8a981-124">Aplikacje Blazor Server domyślnie nie zawierają skonfigurowanej `HttpClient` jako usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="8a981-125">Podaj `HttpClient` aplikację Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="8a981-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="8a981-126">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="8a981-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="8a981-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="8a981-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="8a981-128">Zakres (Serwer Blazor)</span><span class="sxs-lookup"><span data-stu-id="8a981-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="8a981-129">Reprezentuje wystąpienie środowiska wykonawczego JavaScript, w którym są wywoływane wywołania JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8a981-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="8a981-130">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="8a981-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="8a981-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="8a981-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="8a981-132">Zakres (Serwer Blazor)</span><span class="sxs-lookup"><span data-stu-id="8a981-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="8a981-133">Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="8a981-134">Aby uzyskać więcej informacji, zobacz [URI i pomocników stanu nawigacji](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="8a981-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="8a981-135">Dostawca usług niestandardowych nie udostępnia automatycznie usług domyślnych wymienionych w tabeli.</span><span class="sxs-lookup"><span data-stu-id="8a981-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="8a981-136">Jeśli korzystasz z niestandardowego dostawcy usług i wymagasz dowolnej z usług wyświetlanych w tabeli, dodaj wymagane usługi do nowego dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="8a981-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="8a981-137">Dodawanie usług do aplikacji</span><span class="sxs-lookup"><span data-stu-id="8a981-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="8a981-138">Zestaw WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="8a981-138">Blazor WebAssembly</span></span>

<span data-ttu-id="8a981-139">Skonfiguruj usługi dla kolekcji `Main` usług aplikacji w metodzie *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="8a981-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="8a981-140">W poniższym przykładzie implementacja jest zarejestrowana `MyDependency` dla: `IMyDependency`</span><span class="sxs-lookup"><span data-stu-id="8a981-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="8a981-141">Po zbudowaniu hosta usługi są dostępne z głównego zakresu DI przed renderowaniem jakichkolwiek składników.</span><span class="sxs-lookup"><span data-stu-id="8a981-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="8a981-142">Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:</span><span class="sxs-lookup"><span data-stu-id="8a981-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="8a981-143">Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="8a981-144">Opierając się na poprzednim przykładzie, adres URL usługi pogodowej jest przekazywany z domyślnego źródła `InitializeWeatherAsync`konfiguracji (na przykład *appsettings.json)* do:</span><span class="sxs-lookup"><span data-stu-id="8a981-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="8a981-145">Serwer Blazor</span><span class="sxs-lookup"><span data-stu-id="8a981-145">Blazor Server</span></span>

<span data-ttu-id="8a981-146">Po utworzeniu nowej aplikacji `Startup.ConfigureServices` sprawdź metodę:</span><span class="sxs-lookup"><span data-stu-id="8a981-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="8a981-147">Metoda `ConfigureServices` jest przekazywana , <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>który jest listą<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>obiektów deskryptora usługi ( ).</span><span class="sxs-lookup"><span data-stu-id="8a981-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="8a981-148">Usługi są dodawane przez zapewnienie deskryptorów usługi do kolekcji usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="8a981-149">Poniższy przykład przedstawia koncepcję `IDataAccess` z interfejsem `DataAccess`i jego konkretną implementacją:</span><span class="sxs-lookup"><span data-stu-id="8a981-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="8a981-150">Okres istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="8a981-150">Service lifetime</span></span>

<span data-ttu-id="8a981-151">Usługi można skonfigurować z okresami istnienia pokazanymi w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="8a981-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="8a981-152">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="8a981-152">Lifetime</span></span> | <span data-ttu-id="8a981-153">Opis</span><span class="sxs-lookup"><span data-stu-id="8a981-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="8a981-154">Aplikacje WebAssembly nie mają obecnie pojęcia zakresów DI.</span><span class="sxs-lookup"><span data-stu-id="8a981-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="8a981-155">`Scoped`-zarejestrowanych usług `Singleton` zachowują się jak usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="8a981-156">Jednak model Blazor hostingu serwera `Scoped` obsługuje okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="8a981-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="8a981-157">W Blazor aplikacjach serwera rejestracja usługi o określonym zakresie jest o zakresie *do połączenia*.</span><span class="sxs-lookup"><span data-stu-id="8a981-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="8a981-158">Z tego powodu przy użyciu usług o określonym zakresie jest preferowane dla usług, które powinny być ograniczone do bieżącego użytkownika, nawet jeśli bieżącą intencją jest uruchomienie po stronie klienta w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="8a981-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="8a981-159">DI tworzy *pojedyncze wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="8a981-160">Wszystkie składniki wymagające `Singleton` usługi otrzymują wystąpienie tej samej usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="8a981-161">Za każdym razem, gdy składnik `Transient` uzyskuje wystąpienie usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="8a981-162">System DI jest oparty na systemie DI w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a981-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="8a981-163">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8a981-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="8a981-164">Żądanie usługi w składniku</span><span class="sxs-lookup"><span data-stu-id="8a981-164">Request a service in a component</span></span>

<span data-ttu-id="8a981-165">Po dodaniu usług do kolekcji usługi wstrzyknąć usługi do składników przy użyciu [ \@dyrektywy Razor wstrzyknąć.](xref:mvc/views/razor#inject)</span><span class="sxs-lookup"><span data-stu-id="8a981-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="8a981-166">`@inject`posiada dwa parametry:</span><span class="sxs-lookup"><span data-stu-id="8a981-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="8a981-167">Wpisz &ndash; typ usługi do wstrzyknięcia.</span><span class="sxs-lookup"><span data-stu-id="8a981-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="8a981-168">Właściwość &ndash; Nazwa właściwości odbierającej wstrzykniętą usługę aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="8a981-169">Właściwość nie wymaga ręcznego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="8a981-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="8a981-170">Kompilator tworzy właściwość.</span><span class="sxs-lookup"><span data-stu-id="8a981-170">The compiler creates the property.</span></span>

<span data-ttu-id="8a981-171">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8a981-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="8a981-172">Użyj `@inject` wielu instrukcji, aby wstrzyknąć różne usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="8a981-173">W poniższym przykładzie `@inject`pokazano, jak używać .</span><span class="sxs-lookup"><span data-stu-id="8a981-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="8a981-174">Implementacja `Services.IDataAccess` usługi jest wstrzykuje `DataRepository`się do właściwości składnika .</span><span class="sxs-lookup"><span data-stu-id="8a981-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="8a981-175">Należy zauważyć, jak kod `IDataAccess` jest tylko przy użyciu abstrakcji:</span><span class="sxs-lookup"><span data-stu-id="8a981-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="8a981-176">Wewnętrznie wygenerowana właściwość`DataRepository`( ) `InjectAttribute` używa atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8a981-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="8a981-177">Zazwyczaj ten atrybut nie jest używany bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="8a981-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="8a981-178">Jeśli klasa podstawowa jest wymagana dla komponentów i właściwości wtryskiwanych są `InjectAttribute`również wymagane dla klasy podstawowej, należy ręcznie dodać:</span><span class="sxs-lookup"><span data-stu-id="8a981-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="8a981-179">W składnikach pochodzących z `@inject` klasy podstawowej dyrektywa nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="8a981-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="8a981-180">Klasa `InjectAttribute` podstawowa jest wystarczająca:</span><span class="sxs-lookup"><span data-stu-id="8a981-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="8a981-181">Używanie DI w usługach</span><span class="sxs-lookup"><span data-stu-id="8a981-181">Use DI in services</span></span>

<span data-ttu-id="8a981-182">Złożone usługi mogą wymagać dodatkowych usług.</span><span class="sxs-lookup"><span data-stu-id="8a981-182">Complex services might require additional services.</span></span> <span data-ttu-id="8a981-183">W poprzednim przykładzie `DataAccess` może `HttpClient` wymagać usługi domyślnej.</span><span class="sxs-lookup"><span data-stu-id="8a981-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="8a981-184">`@inject`(lub `InjectAttribute`) nie jest dostępny do użytku w usługach.</span><span class="sxs-lookup"><span data-stu-id="8a981-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="8a981-185">Zamiast tego należy użyć *wtrysku konstruktora.*</span><span class="sxs-lookup"><span data-stu-id="8a981-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="8a981-186">Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi.</span><span class="sxs-lookup"><span data-stu-id="8a981-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="8a981-187">Gdy DI tworzy usługę, rozpoznaje usługi, które wymaga w konstruktorze i zapewnia je odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="8a981-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="8a981-188">Wymagania wstępne dotyczące iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="8a981-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="8a981-189">Jeden konstruktor musi istnieć, którego argumenty mogą być spełnione przez DI.</span><span class="sxs-lookup"><span data-stu-id="8a981-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="8a981-190">Dodatkowe parametry nieobjęte DI są dozwolone, jeśli określają wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="8a981-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="8a981-191">Odpowiedni konstruktor musi być *publiczny.*</span><span class="sxs-lookup"><span data-stu-id="8a981-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="8a981-192">Jeden odpowiedni konstruktor musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="8a981-192">One applicable constructor must exist.</span></span> <span data-ttu-id="8a981-193">W przypadku niejednoznaczności DI zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="8a981-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="8a981-194">Klasy składników podstawowych narzędzia do zarządzania zakresem DI</span><span class="sxs-lookup"><span data-stu-id="8a981-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="8a981-195">W aplikacjach ASP.NET Core usługi o określonym zakresie są zazwyczaj ograniczone do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="8a981-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="8a981-196">Po zakończeniu żądania wszelkie usługi o określonym zakresie lub przejściowe są usuwane przez system DI.</span><span class="sxs-lookup"><span data-stu-id="8a981-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="8a981-197">W Blazor aplikacjach serwera zakres żądania trwa przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i o określonym zakresie będą żyły znacznie dłużej niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="8a981-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="8a981-198">W Blazor aplikacjach WebAssembly usługi zarejestrowane z okresem istnienia o określonym zakresie są traktowane jako singletony, więc żyją dłużej niż usługi o określonym zakresie w typowych aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a981-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="8a981-199">Podejście, które ogranicza okres Blazor istnienia usługi `OwningComponentBase` w aplikacjach jest użycie typu.</span><span class="sxs-lookup"><span data-stu-id="8a981-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="8a981-200">`OwningComponentBase`jest typem abstrakcyjnym `ComponentBase` pochodzącym z tego tworzy zakres DI odpowiadający okres istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="8a981-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="8a981-201">Przy użyciu tego zakresu, jest możliwe do korzystania z usług DI o okresie istnienia o określonym zakresie i mieć je na żywo tak długo, jak składnik.</span><span class="sxs-lookup"><span data-stu-id="8a981-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="8a981-202">Gdy składnik zostanie zniszczony, usługi od dostawcy usług o określonym zakresie składnika są również usuwane.</span><span class="sxs-lookup"><span data-stu-id="8a981-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="8a981-203">Może to być przydatne w przypadku usług, które:</span><span class="sxs-lookup"><span data-stu-id="8a981-203">This can be useful for services that:</span></span>

* <span data-ttu-id="8a981-204">Powinny być ponownie w obrębie składnika, jak okres przejściowy jest nieodpowiednie.</span><span class="sxs-lookup"><span data-stu-id="8a981-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="8a981-205">Nie powinny być współużytkowane przez składniki, jak okres istnienia singleton jest niewłaściwe.</span><span class="sxs-lookup"><span data-stu-id="8a981-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="8a981-206">Dostępne są `OwningComponentBase` dwie wersje typu:</span><span class="sxs-lookup"><span data-stu-id="8a981-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="8a981-207">`OwningComponentBase`jest abstrakcyjnym, jednorazowym `ComponentBase` dzieckiem typu `ScopedServices` z chroniona właściwość typu `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="8a981-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="8a981-208">Ten dostawca może służyć do rozpoznawania usług, które są ograniczone do okresu istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="8a981-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="8a981-209">Usługi DI wstrzyknięte `InjectAttribute` do`[Inject]`składnika przy użyciu `@inject` lub ( ) nie są tworzone w zakresie składnika.</span><span class="sxs-lookup"><span data-stu-id="8a981-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="8a981-210">Aby korzystać z zakresu składnika, usługi `ScopedServices.GetRequiredService` `ScopedServices.GetService`muszą być rozwiązane za pomocą lub .</span><span class="sxs-lookup"><span data-stu-id="8a981-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="8a981-211">Wszystkie usługi rozwiązane `ScopedServices` przy użyciu dostawcy mają swoje zależności dostarczone z tego samego zakresu.</span><span class="sxs-lookup"><span data-stu-id="8a981-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="8a981-212">`OwningComponentBase<T>`pochodzi z `OwningComponentBase` i dodaje `Service` właściwość, `T` która zwraca wystąpienie z dostawcy DI o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="8a981-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="8a981-213">Ten typ jest wygodnym sposobem dostępu do usług `IServiceProvider` o określonym zakresie bez użycia wystąpienia, gdy istnieje jedna usługa podstawowa, którą aplikacja wymaga z kontenera DI przy użyciu zakresu składnika.</span><span class="sxs-lookup"><span data-stu-id="8a981-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="8a981-214">Właściwość `ScopedServices` jest dostępna, dzięki czemu aplikacja może uzyskać usługi innych typów, jeśli to konieczne.</span><span class="sxs-lookup"><span data-stu-id="8a981-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="8a981-215">Korzystanie z entity framework DbContext z DI</span><span class="sxs-lookup"><span data-stu-id="8a981-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="8a981-216">Jednym z typowych typów usług do pobrania z DI `DbContext` w aplikacjach sieci web jest Entity Framework (EF) obiektów.</span><span class="sxs-lookup"><span data-stu-id="8a981-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="8a981-217">Rejestrowanie usług `IServiceCollection.AddDbContext` EF `DbContext` przy użyciu dodaje jako usługi o określonym zakresie domyślnie.</span><span class="sxs-lookup"><span data-stu-id="8a981-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="8a981-218">Rejestrowanie jako usługa o określonym zakresie Blazor może prowadzić `DbContext` do problemów w aplikacjach, ponieważ powoduje, że wystąpienia mają być długotrwałe i udostępniane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a981-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="8a981-219">`DbContext`nie jest bezpieczny dla wątków i nie może być używany jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="8a981-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="8a981-220">W zależności od `OwningComponentBase` aplikacji, za pomocą `DbContext` ograniczenia zakresu do jednego składnika *może* rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="8a981-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="8a981-221">Jeśli `DbContext` składnik nie używa równolegle, wyprowadzanie składnika `OwningComponentBase` z i `DbContext` `ScopedServices` pobieranie z jest wystarczające, ponieważ zapewnia, że:</span><span class="sxs-lookup"><span data-stu-id="8a981-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="8a981-222">Oddzielne składniki nie współużytkuje `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="8a981-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="8a981-223">Żyje `DbContext` tylko tak długo, jak składnik w zależności od niego.</span><span class="sxs-lookup"><span data-stu-id="8a981-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="8a981-224">Jeśli pojedynczy składnik może `DbContext` używać jednocześnie (na przykład za każdym razem, gdy `OwningComponentBase` użytkownik wybierze przycisk), nawet przy użyciu nie unika problemów z równoczesnych operacji EF.</span><span class="sxs-lookup"><span data-stu-id="8a981-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="8a981-225">W takim przypadku należy `DbContext` użyć innego dla każdej operacji logicznego EF.</span><span class="sxs-lookup"><span data-stu-id="8a981-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="8a981-226">Użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="8a981-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="8a981-227">Utwórz `DbContext` bezpośrednio `DbContextOptions<TContext>` jako argument, który może być pobierany z DI i jest bezpieczny dla wątków.</span><span class="sxs-lookup"><span data-stu-id="8a981-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

* <span data-ttu-id="8a981-228">Zarejestruj `DbContext` w kontenerze usługi z przejściowym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="8a981-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="8a981-229">Podczas rejestrowania kontekstu `ServiceLifetime.Transient`należy użyć pliku .</span><span class="sxs-lookup"><span data-stu-id="8a981-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="8a981-230">Metoda `AddDbContext` rozszerzenia przyjmuje dwa opcjonalne parametry typu `ServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="8a981-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="8a981-231">Aby użyć tego podejścia, tylko `contextLifetime` `ServiceLifetime.Transient`parametr musi być .</span><span class="sxs-lookup"><span data-stu-id="8a981-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="8a981-232">`optionsLifetime`może zachować domyślną wartość . `ServiceLifetime.Scoped`</span><span class="sxs-lookup"><span data-stu-id="8a981-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="8a981-233">Przejściowy `DbContext` może być wstrzykiwany normalnie (przy użyciu) `@inject`do składników, które nie będą wykonywać wiele operacji EF równolegle.</span><span class="sxs-lookup"><span data-stu-id="8a981-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="8a981-234">Te, które mogą wykonywać wiele operacji `DbContext` EF jednocześnie `IServiceProvider.GetRequiredService`można zażądać oddzielnych obiektów dla każdej operacji równoległej przy użyciu .</span><span class="sxs-lookup"><span data-stu-id="8a981-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

## <a name="additional-resources"></a><span data-ttu-id="8a981-235">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8a981-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
