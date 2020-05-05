---
title: ASP.NET Core Blazor wstrzykiwania zależności
author: guardrex
description: Zobacz, Blazor jak aplikacje mogą wstrzyknąć usługi do składników programu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 742f3c5ea26fab5e168f162a0e133da05fd74a74
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767122"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="599ef-103">ASP.NET Core iniekcja zależności Blazor</span><span class="sxs-lookup"><span data-stu-id="599ef-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="599ef-104">Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="599ef-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="599ef-105">Blazor obsługuje [iniekcję zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="599ef-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="599ef-106">Aplikacje mogą używać wbudowanych usług, wprowadzając je do składników programu.</span><span class="sxs-lookup"><span data-stu-id="599ef-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="599ef-107">Aplikacje mogą także definiować i rejestrować niestandardowe usługi i udostępniać je w całej aplikacji za pomocą funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="599ef-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="599ef-108">DI jest techniką uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="599ef-109">Może to być przydatne w aplikacjach Blazor do:</span><span class="sxs-lookup"><span data-stu-id="599ef-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="599ef-110">Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanej jako *pojedyncze usługi.*</span><span class="sxs-lookup"><span data-stu-id="599ef-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="599ef-111">Oddziel składniki od klas konkretnych usług za pomocą abstrakcji odwołań.</span><span class="sxs-lookup"><span data-stu-id="599ef-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="599ef-112">Rozważmy na przykład interfejs `IDataAccess` do uzyskiwania dostępu do danych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="599ef-113">Interfejs jest implementowany przez konkretną `DataAccess` klasę i zarejestrowany jako usługa w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="599ef-114">Gdy składnik używa elementu DI do odbierania `IDataAccess` implementacji, składnik nie jest połączony z konkretnym typem.</span><span class="sxs-lookup"><span data-stu-id="599ef-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="599ef-115">Implementacja może zostać zamieniony, być może dla implementacji makiety w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="599ef-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="599ef-116">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="599ef-116">Default services</span></span>

<span data-ttu-id="599ef-117">Domyślne usługi są automatycznie dodawane do kolekcji usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="599ef-118">Usługa</span><span class="sxs-lookup"><span data-stu-id="599ef-118">Service</span></span> | <span data-ttu-id="599ef-119">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="599ef-119">Lifetime</span></span> | <span data-ttu-id="599ef-120">Opis</span><span class="sxs-lookup"><span data-stu-id="599ef-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="599ef-121">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="599ef-121">Transient</span></span> | <span data-ttu-id="599ef-122">Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="599ef-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="599ef-123">Wystąpienie elementu `HttpClient` w aplikacji Blazor webassembly używa przeglądarki do obsługi ruchu HTTP w tle.</span><span class="sxs-lookup"><span data-stu-id="599ef-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="599ef-124">Aplikacje serwera Blazor nie domyślnie zawierają `HttpClient` skonfigurowane jako usługa.</span><span class="sxs-lookup"><span data-stu-id="599ef-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="599ef-125">`HttpClient` Udostępnianie aplikacji serwera Blazor.</span><span class="sxs-lookup"><span data-stu-id="599ef-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="599ef-126">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="599ef-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="599ef-127">Singleton (Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="599ef-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="599ef-128">W zakresie (serwer Blazor)</span><span class="sxs-lookup"><span data-stu-id="599ef-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="599ef-129">Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="599ef-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="599ef-130">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="599ef-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="599ef-131">Singleton (Blazor webassembly)</span><span class="sxs-lookup"><span data-stu-id="599ef-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="599ef-132">W zakresie (serwer Blazor)</span><span class="sxs-lookup"><span data-stu-id="599ef-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="599ef-133">Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="599ef-134">Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="599ef-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="599ef-135">Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli.</span><span class="sxs-lookup"><span data-stu-id="599ef-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="599ef-136">W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="599ef-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="599ef-137">Dodawanie usług do aplikacji</span><span class="sxs-lookup"><span data-stu-id="599ef-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="599ef-138">Zestaw WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="599ef-138">Blazor WebAssembly</span></span>

<span data-ttu-id="599ef-139">Skonfiguruj usługi dla kolekcji usług aplikacji w `Main` metodzie *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="599ef-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="599ef-140">W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="599ef-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="599ef-141">Po skompilowaniu hosta usługi można uzyskać dostęp z poziomu głównego DI zakresu przed renderowaniem wszystkich składników.</span><span class="sxs-lookup"><span data-stu-id="599ef-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="599ef-142">Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:</span><span class="sxs-lookup"><span data-stu-id="599ef-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="599ef-143">Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="599ef-144">W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład *appSettings. JSON*) do `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="599ef-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="599ef-145">Serwer Blazor</span><span class="sxs-lookup"><span data-stu-id="599ef-145">Blazor Server</span></span>

<span data-ttu-id="599ef-146">Po utworzeniu nowej aplikacji zapoznaj się z `Startup.ConfigureServices` tą metodą:</span><span class="sxs-lookup"><span data-stu-id="599ef-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="599ef-147">`ConfigureServices` Metoda jest przenoszona a <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, która jest listą obiektów deskryptora usługi (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="599ef-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="599ef-148">Usługi są dodawane przez dostarczenie deskryptorów usługi do kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="599ef-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="599ef-149">Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją: `DataAccess`</span><span class="sxs-lookup"><span data-stu-id="599ef-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="599ef-150">Okres istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="599ef-150">Service lifetime</span></span>

<span data-ttu-id="599ef-151">Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="599ef-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="599ef-152">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="599ef-152">Lifetime</span></span> | <span data-ttu-id="599ef-153">Opis</span><span class="sxs-lookup"><span data-stu-id="599ef-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="599ef-154">Aplikacje webassembly nie mają obecnie koncepcji DI Scopes.</span><span class="sxs-lookup"><span data-stu-id="599ef-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="599ef-155">`Scoped`-zarejestrowane usługi zachowują `Singleton` się jak usługi.</span><span class="sxs-lookup"><span data-stu-id="599ef-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="599ef-156">Jednak model hostingu Blazor serwera obsługuje `Scoped` okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="599ef-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="599ef-157">W Blazor przypadku aplikacji serwerowych Rejestracja usługi w zakresie jest objęta zakresem *połączenia*.</span><span class="sxs-lookup"><span data-stu-id="599ef-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="599ef-158">Z tego powodu użycie usług objętych zakresem jest preferowane dla usług, które powinny być objęte zakresem bieżącego użytkownika, nawet jeśli bieżącym celem jest uruchomienie po stronie klienta w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="599ef-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="599ef-159">DI tworzy *pojedyncze wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="599ef-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="599ef-160">Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi.</span><span class="sxs-lookup"><span data-stu-id="599ef-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="599ef-161">Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="599ef-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="599ef-162">System DI jest oparty na systemie DI w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="599ef-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="599ef-163">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="599ef-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="599ef-164">Żądanie usługi w składniku</span><span class="sxs-lookup"><span data-stu-id="599ef-164">Request a service in a component</span></span>

<span data-ttu-id="599ef-165">Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą dyrektywy [ \@wstrzykiwania](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="599ef-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="599ef-166">`@inject`ma dwa parametry:</span><span class="sxs-lookup"><span data-stu-id="599ef-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="599ef-167">Wpisz &ndash; typ usługi do dodania.</span><span class="sxs-lookup"><span data-stu-id="599ef-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="599ef-168">Właściwość &ndash; nazwa właściwości otrzymującej wstrzykiwanej usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="599ef-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="599ef-169">Właściwość nie wymaga ręcznego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="599ef-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="599ef-170">Kompilator tworzy właściwość.</span><span class="sxs-lookup"><span data-stu-id="599ef-170">The compiler creates the property.</span></span>

<span data-ttu-id="599ef-171">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="599ef-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="599ef-172">Użyj wielu `@inject` instrukcji, aby wstrzyknąć różne usługi.</span><span class="sxs-lookup"><span data-stu-id="599ef-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="599ef-173">Poniższy przykład pokazuje, jak używać `@inject`.</span><span class="sxs-lookup"><span data-stu-id="599ef-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="599ef-174">Implementowanie `Services.IDataAccess` usługi jest wstrzykiwane do właściwości `DataRepository`składnika.</span><span class="sxs-lookup"><span data-stu-id="599ef-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="599ef-175">Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:</span><span class="sxs-lookup"><span data-stu-id="599ef-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="599ef-176">Wewnętrznie wygenerowana Właściwość (`DataRepository`) używa `InjectAttribute` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="599ef-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="599ef-177">Zazwyczaj ten atrybut nie jest używany bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="599ef-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="599ef-178">Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać `InjectAttribute`:</span><span class="sxs-lookup"><span data-stu-id="599ef-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="599ef-179">W składnikach pochodnych klasy bazowej `@inject` dyrektywa nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="599ef-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="599ef-180">`InjectAttribute` Klasa bazowa jest wystarczająca:</span><span class="sxs-lookup"><span data-stu-id="599ef-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="599ef-181">Korzystanie z usług DI w</span><span class="sxs-lookup"><span data-stu-id="599ef-181">Use DI in services</span></span>

<span data-ttu-id="599ef-182">Złożone usługi mogą wymagać dodatkowych usług.</span><span class="sxs-lookup"><span data-stu-id="599ef-182">Complex services might require additional services.</span></span> <span data-ttu-id="599ef-183">W poprzednim przykładzie `DataAccess` może być wymagana usługa `HttpClient` domyślna.</span><span class="sxs-lookup"><span data-stu-id="599ef-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="599ef-184">`@inject`(lub `InjectAttribute`) nie jest dostępny do użytku w usługach.</span><span class="sxs-lookup"><span data-stu-id="599ef-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="599ef-185">Zamiast tego należy użyć *iniekcji konstruktora* .</span><span class="sxs-lookup"><span data-stu-id="599ef-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="599ef-186">Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi.</span><span class="sxs-lookup"><span data-stu-id="599ef-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="599ef-187">Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="599ef-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="599ef-188">Wymagania wstępne dotyczące iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="599ef-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="599ef-189">Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI.</span><span class="sxs-lookup"><span data-stu-id="599ef-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="599ef-190">Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="599ef-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="599ef-191">Odpowiedni Konstruktor musi być *publiczny*.</span><span class="sxs-lookup"><span data-stu-id="599ef-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="599ef-192">Musi istnieć jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="599ef-192">One applicable constructor must exist.</span></span> <span data-ttu-id="599ef-193">W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="599ef-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="599ef-194">Klasy składników podstawowych narzędzi do zarządzania DI zakresem</span><span class="sxs-lookup"><span data-stu-id="599ef-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="599ef-195">W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="599ef-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="599ef-196">Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI.</span><span class="sxs-lookup"><span data-stu-id="599ef-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="599ef-197">W Blazor obszarze aplikacje serwera zakres żądań jest używany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="599ef-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="599ef-198">W Blazor aplikacjach webassembly usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="599ef-199">Zastosowanie tego Blazor `OwningComponentBase` typu w aplikacjach ogranicza okres istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="599ef-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="599ef-200">`OwningComponentBase`jest abstrakcyjnym typem pochodnym `ComponentBase` , który tworzy zakres di odpowiadający okresowi istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="599ef-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="599ef-201">Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika.</span><span class="sxs-lookup"><span data-stu-id="599ef-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="599ef-202">Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="599ef-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="599ef-203">Może to być przydatne w przypadku usług, które:</span><span class="sxs-lookup"><span data-stu-id="599ef-203">This can be useful for services that:</span></span>

* <span data-ttu-id="599ef-204">Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="599ef-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="599ef-205">Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="599ef-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="599ef-206">Dostępne są dwie wersje `OwningComponentBase` typu:</span><span class="sxs-lookup"><span data-stu-id="599ef-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="599ef-207">`OwningComponentBase`jest abstrakcyjnym, jednorazowym elementem podrzędnym `ComponentBase` typu z właściwością chronioną `IServiceProvider` `ScopedServices` typu.</span><span class="sxs-lookup"><span data-stu-id="599ef-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="599ef-208">Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="599ef-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="599ef-209">Usługi di są wstrzykiwane do składnika przy `@inject` użyciu lub `InjectAttribute` (`[Inject]`) nie są tworzone w zakresie składnika.</span><span class="sxs-lookup"><span data-stu-id="599ef-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="599ef-210">Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane `ScopedServices.GetRequiredService` przy `ScopedServices.GetService`użyciu lub.</span><span class="sxs-lookup"><span data-stu-id="599ef-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="599ef-211">Wszystkie usługi rozpoznane przy użyciu `ScopedServices` dostawcy mają swoje zależności z tego samego zakresu.</span><span class="sxs-lookup"><span data-stu-id="599ef-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="599ef-212">`OwningComponentBase<T>`pochodzi z `OwningComponentBase` i dodaje właściwość `Service` zwracającą wystąpienie `T` z dostawcy i zakresu.</span><span class="sxs-lookup"><span data-stu-id="599ef-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="599ef-213">Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia `IServiceProvider` , gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika.</span><span class="sxs-lookup"><span data-stu-id="599ef-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="599ef-214">Ta `ScopedServices` właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="599ef-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="599ef-215">Korzystanie z Entity Framework DbContext z elementu DI</span><span class="sxs-lookup"><span data-stu-id="599ef-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="599ef-216">Jednym z typowych typów usług pobieranych z aplikacji sieci Web typu "i" jest `DbContext` Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="599ef-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="599ef-217">Rejestrowanie usług EF przy `IServiceCollection.AddDbContext` użyciu domyślnie `DbContext` dodaje usługę jako objętą zakresem.</span><span class="sxs-lookup"><span data-stu-id="599ef-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="599ef-218">Rejestracja w ramach usługi w zakresie może prowadzić do problemów w Blazor aplikacjach, ponieważ powoduje `DbContext` to, że wystąpienia są długotrwałe i udostępniane w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="599ef-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="599ef-219">`DbContext`nie jest bezpieczne dla wątków i nie może być używane współbieżnie.</span><span class="sxs-lookup"><span data-stu-id="599ef-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="599ef-220">W zależności od aplikacji korzystanie `OwningComponentBase` z programu w celu ograniczenia zakresu `DbContext` jednego składnika *może* rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="599ef-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="599ef-221">Jeśli składnik `DbContext` nie korzysta równolegle, wyprowadza składnik z `OwningComponentBase` i pobieranie `DbContext` z `ScopedServices` jest wystarczające, ponieważ gwarantuje to:</span><span class="sxs-lookup"><span data-stu-id="599ef-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="599ef-222">Oddzielne składniki nie współdzielą `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="599ef-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="599ef-223">`DbContext` Mieszka tylko tak długo, jak i w zależności od tego, czy jest to składnik.</span><span class="sxs-lookup"><span data-stu-id="599ef-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="599ef-224">Jeśli pojedynczy składnik może korzystać z `DbContext` współbieżnie (na przykład za każdym razem, gdy użytkownik wybierze przycisk), nawet przy użyciu `OwningComponentBase` nie należy unikać problemów z jednoczesnymi operacjami EF.</span><span class="sxs-lookup"><span data-stu-id="599ef-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="599ef-225">W takim przypadku należy użyć innej `DbContext` dla każdej operacji logicznej EF.</span><span class="sxs-lookup"><span data-stu-id="599ef-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="599ef-226">Użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="599ef-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="599ef-227">Utwórz `DbContext` bezpośrednio przy użyciu `DbContextOptions<TContext>` jako argument, który można pobrać z elementu "i" jest bezpieczny wątkowo.</span><span class="sxs-lookup"><span data-stu-id="599ef-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="599ef-228">Zarejestruj `DbContext` w kontenerze usługi z przejściowym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="599ef-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="599ef-229">Podczas rejestrowania kontekstu użyj `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="599ef-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="599ef-230">Metoda `AddDbContext` rozszerzenia przyjmuje dwa opcjonalne parametry typu `ServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="599ef-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="599ef-231">Aby skorzystać z tej metody, należy `contextLifetime` podać `ServiceLifetime.Transient`tylko parametr.</span><span class="sxs-lookup"><span data-stu-id="599ef-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="599ef-232">`optionsLifetime`może zachować wartość domyślną `ServiceLifetime.Scoped`.</span><span class="sxs-lookup"><span data-stu-id="599ef-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="599ef-233">Przejściowy `DbContext` można wstrzyknąć jako normalny (przy użyciu `@inject`) do składników, które nie wykonują równolegle wielu operacji EF.</span><span class="sxs-lookup"><span data-stu-id="599ef-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="599ef-234">Te, które mogą wykonywać wiele operacji EF jednocześnie, mogą `DbContext` zażądać oddzielnych obiektów dla każdej `IServiceProvider.GetRequiredService`operacji równoległej przy użyciu.</span><span class="sxs-lookup"><span data-stu-id="599ef-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="599ef-235">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="599ef-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
