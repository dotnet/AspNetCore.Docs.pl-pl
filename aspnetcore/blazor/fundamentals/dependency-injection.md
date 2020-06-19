---
title: ASP.NET Core Blazor wstrzykiwania zależności
author: guardrex
description: Dowiedz się, jak Blazor aplikacje mogą wstrzyknąć usługi do składników programu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: b4ac0dbc6dabdeff4689544f2e11278b8302c553
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103854"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="d29c9-103">ASP.NET Core Blazor wstrzykiwania zależności</span><span class="sxs-lookup"><span data-stu-id="d29c9-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="d29c9-104">Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="d29c9-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="d29c9-105">obsługuje [iniekcję zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d29c9-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d29c9-106">Aplikacje mogą używać wbudowanych usług, wprowadzając je do składników programu.</span><span class="sxs-lookup"><span data-stu-id="d29c9-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="d29c9-107">Aplikacje mogą także definiować i rejestrować niestandardowe usługi i udostępniać je w całej aplikacji za pomocą funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="d29c9-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="d29c9-108">DI jest techniką uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="d29c9-109">Może to być przydatne w Blazor aplikacjach, aby:</span><span class="sxs-lookup"><span data-stu-id="d29c9-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="d29c9-110">Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanej jako *pojedyncze usługi.*</span><span class="sxs-lookup"><span data-stu-id="d29c9-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="d29c9-111">Oddziel składniki od klas konkretnych usług za pomocą abstrakcji odwołań.</span><span class="sxs-lookup"><span data-stu-id="d29c9-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="d29c9-112">Rozważmy na przykład interfejs `IDataAccess` do uzyskiwania dostępu do danych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="d29c9-113">Interfejs jest implementowany przez konkretną `DataAccess` klasę i zarejestrowany jako usługa w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="d29c9-114">Gdy składnik używa elementu DI do odbierania `IDataAccess` implementacji, składnik nie jest połączony z konkretnym typem.</span><span class="sxs-lookup"><span data-stu-id="d29c9-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="d29c9-115">Implementacja może zostać zamieniony, być może dla implementacji makiety w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="d29c9-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="d29c9-116">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="d29c9-116">Default services</span></span>

<span data-ttu-id="d29c9-117">Domyślne usługi są automatycznie dodawane do kolekcji usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="d29c9-118">Usługa</span><span class="sxs-lookup"><span data-stu-id="d29c9-118">Service</span></span> | <span data-ttu-id="d29c9-119">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="d29c9-119">Lifetime</span></span> | <span data-ttu-id="d29c9-120">Opis</span><span class="sxs-lookup"><span data-stu-id="d29c9-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="d29c9-121">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="d29c9-121">Transient</span></span> | <span data-ttu-id="d29c9-122">Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="d29c9-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="d29c9-123">Wystąpienie elementu <xref:System.Net.Http.HttpClient> w aplikacji sieci Blazor webassembly używa przeglądarki do obsługi ruchu HTTP w tle.</span><span class="sxs-lookup"><span data-stu-id="d29c9-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="d29c9-124">Aplikacje serwera nie domyślnie zawierają <xref:System.Net.Http.HttpClient> skonfigurowane jako usługa.</span><span class="sxs-lookup"><span data-stu-id="d29c9-124"> Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="d29c9-125">Udostępnianie <xref:System.Net.Http.HttpClient> Blazor aplikacji serwerowej.</span><span class="sxs-lookup"><span data-stu-id="d29c9-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="d29c9-126">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="d29c9-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="d29c9-127">Pojedyncze ( Blazor zestaw webassembly)</span><span class="sxs-lookup"><span data-stu-id="d29c9-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="d29c9-128">Zakres ( Blazor serwer)</span><span class="sxs-lookup"><span data-stu-id="d29c9-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="d29c9-129">Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d29c9-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="d29c9-130">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="d29c9-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="d29c9-131">Pojedyncze ( Blazor zestaw webassembly)</span><span class="sxs-lookup"><span data-stu-id="d29c9-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="d29c9-132">Zakres ( Blazor serwer)</span><span class="sxs-lookup"><span data-stu-id="d29c9-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="d29c9-133">Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="d29c9-134">Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="d29c9-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="d29c9-135">Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli.</span><span class="sxs-lookup"><span data-stu-id="d29c9-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="d29c9-136">W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="d29c9-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="d29c9-137">Dodawanie usług do aplikacji</span><span class="sxs-lookup"><span data-stu-id="d29c9-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="d29c9-138">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="d29c9-138"> WebAssembly</span></span>

<span data-ttu-id="d29c9-139">Skonfiguruj usługi dla kolekcji usług aplikacji w `Main` metodzie *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="d29c9-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="d29c9-140">W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d29c9-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="d29c9-141">Po skompilowaniu hosta usługi można uzyskać dostęp z poziomu głównego DI zakresu przed renderowaniem wszystkich składników.</span><span class="sxs-lookup"><span data-stu-id="d29c9-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="d29c9-142">Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:</span><span class="sxs-lookup"><span data-stu-id="d29c9-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="d29c9-143">Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="d29c9-144">W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład *appsettings.json*) do `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="d29c9-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="d29c9-145">Server</span><span class="sxs-lookup"><span data-stu-id="d29c9-145"> Server</span></span>

<span data-ttu-id="d29c9-146">Po utworzeniu nowej aplikacji zapoznaj się z tą `Startup.ConfigureServices` metodą:</span><span class="sxs-lookup"><span data-stu-id="d29c9-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="d29c9-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda jest przenoszona <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a, która jest listą obiektów deskryptora usługi ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="d29c9-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="d29c9-148">Usługi są dodawane przez dostarczenie deskryptorów usługi do kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="d29c9-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="d29c9-149">Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="d29c9-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="d29c9-150">Okres istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="d29c9-150">Service lifetime</span></span>

<span data-ttu-id="d29c9-151">Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="d29c9-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="d29c9-152">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="d29c9-152">Lifetime</span></span> | <span data-ttu-id="d29c9-153">Opis</span><span class="sxs-lookup"><span data-stu-id="d29c9-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor<span data-ttu-id="d29c9-154">Aplikacje webassembly nie mają obecnie koncepcji DI Scopes.</span><span class="sxs-lookup"><span data-stu-id="d29c9-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="d29c9-155">`Scoped`-zarejestrowane usługi zachowują się jak `Singleton` usługi.</span><span class="sxs-lookup"><span data-stu-id="d29c9-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="d29c9-156">Jednak Blazor model hostingu serwera obsługuje `Scoped` okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="d29c9-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="d29c9-157">W przypadku Blazor aplikacji serwerowych Rejestracja usługi w zakresie jest objęta zakresem *połączenia*.</span><span class="sxs-lookup"><span data-stu-id="d29c9-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="d29c9-158">Z tego powodu użycie usług objętych zakresem jest preferowane dla usług, które powinny być objęte zakresem bieżącego użytkownika, nawet jeśli bieżącym celem jest uruchomienie po stronie klienta w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="d29c9-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="d29c9-159">DI tworzy *pojedyncze wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="d29c9-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="d29c9-160">Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi.</span><span class="sxs-lookup"><span data-stu-id="d29c9-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="d29c9-161">Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="d29c9-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="d29c9-162">System DI jest oparty na systemie DI w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d29c9-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="d29c9-163">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d29c9-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="d29c9-164">Żądanie usługi w składniku</span><span class="sxs-lookup"><span data-stu-id="d29c9-164">Request a service in a component</span></span>

<span data-ttu-id="d29c9-165">Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą dyrektywy [ \@ wstrzykiwania](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="d29c9-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="d29c9-166">[`@inject`](xref:mvc/views/razor#inject)ma dwa parametry:</span><span class="sxs-lookup"><span data-stu-id="d29c9-166">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="d29c9-167">Typ: typ usługi do dodania.</span><span class="sxs-lookup"><span data-stu-id="d29c9-167">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="d29c9-168">Property: Nazwa właściwości otrzymującej wstrzykiwaną usługę App Service.</span><span class="sxs-lookup"><span data-stu-id="d29c9-168">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="d29c9-169">Właściwość nie wymaga ręcznego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="d29c9-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="d29c9-170">Kompilator tworzy właściwość.</span><span class="sxs-lookup"><span data-stu-id="d29c9-170">The compiler creates the property.</span></span>

<span data-ttu-id="d29c9-171">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d29c9-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="d29c9-172">Użyj wielu [`@inject`](xref:mvc/views/razor#inject) instrukcji, aby wstrzyknąć różne usługi.</span><span class="sxs-lookup"><span data-stu-id="d29c9-172">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="d29c9-173">Poniższy przykład pokazuje, jak używać [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="d29c9-173">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="d29c9-174">Implementowanie usługi `Services.IDataAccess` jest wstrzykiwane do właściwości składnika `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="d29c9-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="d29c9-175">Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:</span><span class="sxs-lookup"><span data-stu-id="d29c9-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="d29c9-176">Wewnętrznie wygenerowana Właściwość ( `DataRepository` ) używa [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="d29c9-176">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="d29c9-177">Zazwyczaj ten atrybut nie jest używany bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="d29c9-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="d29c9-178">Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut:</span><span class="sxs-lookup"><span data-stu-id="d29c9-178">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="d29c9-179">W składnikach pochodnych klasy bazowej [`@inject`](xref:mvc/views/razor#inject) dyrektywa nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="d29c9-179">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="d29c9-180"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Klasa bazowa jest wystarczająca:</span><span class="sxs-lookup"><span data-stu-id="d29c9-180">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="d29c9-181">Korzystanie z usług DI w</span><span class="sxs-lookup"><span data-stu-id="d29c9-181">Use DI in services</span></span>

<span data-ttu-id="d29c9-182">Złożone usługi mogą wymagać dodatkowych usług.</span><span class="sxs-lookup"><span data-stu-id="d29c9-182">Complex services might require additional services.</span></span> <span data-ttu-id="d29c9-183">W poprzednim przykładzie `DataAccess` może być wymagana <xref:System.Net.Http.HttpClient> Usługa domyślna.</span><span class="sxs-lookup"><span data-stu-id="d29c9-183">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="d29c9-184">[`@inject`](xref:mvc/views/razor#inject)(lub [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut) nie jest dostępny do użytku w usługach.</span><span class="sxs-lookup"><span data-stu-id="d29c9-184">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="d29c9-185">Zamiast tego należy użyć *iniekcji konstruktora* .</span><span class="sxs-lookup"><span data-stu-id="d29c9-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="d29c9-186">Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi.</span><span class="sxs-lookup"><span data-stu-id="d29c9-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="d29c9-187">Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="d29c9-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="d29c9-188">Wymagania wstępne dotyczące iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="d29c9-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="d29c9-189">Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI.</span><span class="sxs-lookup"><span data-stu-id="d29c9-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="d29c9-190">Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="d29c9-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="d29c9-191">Odpowiedni Konstruktor musi być *publiczny*.</span><span class="sxs-lookup"><span data-stu-id="d29c9-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="d29c9-192">Musi istnieć jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="d29c9-192">One applicable constructor must exist.</span></span> <span data-ttu-id="d29c9-193">W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="d29c9-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="d29c9-194">Klasy składników podstawowych narzędzi do zarządzania DI zakresem</span><span class="sxs-lookup"><span data-stu-id="d29c9-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="d29c9-195">W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="d29c9-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="d29c9-196">Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI.</span><span class="sxs-lookup"><span data-stu-id="d29c9-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="d29c9-197">W obszarze Blazor aplikacje serwera zakres żądań jest używany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="d29c9-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="d29c9-198">W Blazor aplikacjach webassembly usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="d29c9-199">Zastosowanie tego typu w aplikacjach ogranicza okres istnienia usługi Blazor <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-199">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="d29c9-200"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>jest abstrakcyjnym typem pochodnym <xref:Microsoft.AspNetCore.Components.ComponentBase> , który tworzy zakres di odpowiadający okresowi istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="d29c9-200"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="d29c9-201">Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika.</span><span class="sxs-lookup"><span data-stu-id="d29c9-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="d29c9-202">Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="d29c9-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="d29c9-203">Może to być przydatne w przypadku usług, które:</span><span class="sxs-lookup"><span data-stu-id="d29c9-203">This can be useful for services that:</span></span>

* <span data-ttu-id="d29c9-204">Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="d29c9-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="d29c9-205">Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="d29c9-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="d29c9-206">Dostępne są dwie wersje <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typu:</span><span class="sxs-lookup"><span data-stu-id="d29c9-206">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="d29c9-207"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>jest abstrakcyjnym, jednorazowym elementem podrzędnym <xref:Microsoft.AspNetCore.Components.ComponentBase> typu z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> właściwością chronioną typu <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-207"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d29c9-208">Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="d29c9-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="d29c9-209">Program DI Services wprowadzany do składnika przy użyciu [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu lub nie jest tworzony w zakresie składnika.</span><span class="sxs-lookup"><span data-stu-id="d29c9-209">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="d29c9-210">Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> lub <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-210">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="d29c9-211">Wszystkie usługi rozpoznane przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> dostawcy mają swoje zależności z tego samego zakresu.</span><span class="sxs-lookup"><span data-stu-id="d29c9-211">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="d29c9-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>pochodzi z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i dodaje <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> Właściwość zwracającą wystąpienie `T` z dostawcy i zakresu.</span><span class="sxs-lookup"><span data-stu-id="d29c9-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="d29c9-213">Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia, <xref:System.IServiceProvider> gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika.</span><span class="sxs-lookup"><span data-stu-id="d29c9-213">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="d29c9-214">Ta <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> Właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="d29c9-214">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="d29c9-215">Korzystanie z Entity Framework DbContext z elementu DI</span><span class="sxs-lookup"><span data-stu-id="d29c9-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="d29c9-216">Jednym z typowych typów usług pobieranych z aplikacji sieci Web typu "i" jest Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="d29c9-217">Rejestrowanie usług EF przy użyciu <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> <xref:Microsoft.EntityFrameworkCore.DbContext> Domyślnie dodaje usługę jako objętą zakresem.</span><span class="sxs-lookup"><span data-stu-id="d29c9-217">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="d29c9-218">Rejestracja w ramach usługi w zakresie może prowadzić do problemów w Blazor aplikacjach, ponieważ powoduje to <xref:Microsoft.EntityFrameworkCore.DbContext> , że wystąpienia są długotrwałe i udostępniane w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d29c9-218">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="d29c9-219"><xref:Microsoft.EntityFrameworkCore.DbContext>nie jest bezpieczne dla wątków i nie może być używane współbieżnie.</span><span class="sxs-lookup"><span data-stu-id="d29c9-219"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="d29c9-220">W zależności od aplikacji korzystanie z programu <xref:Microsoft.AspNetCore.Components.OwningComponentBase> w celu ograniczenia zakresu jednego <xref:Microsoft.EntityFrameworkCore.DbContext> składnika *może* rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="d29c9-220">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="d29c9-221">Jeśli składnik nie korzysta <xref:Microsoft.EntityFrameworkCore.DbContext> równolegle, wyprowadza składnik z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i pobieranie <xref:Microsoft.EntityFrameworkCore.DbContext> z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> jest wystarczające, ponieważ gwarantuje to:</span><span class="sxs-lookup"><span data-stu-id="d29c9-221">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="d29c9-222">Oddzielne składniki nie współdzielą <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-222">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="d29c9-223"><xref:Microsoft.EntityFrameworkCore.DbContext>Mieszka tylko tak długo, jak i w zależności od tego, czy jest to składnik.</span><span class="sxs-lookup"><span data-stu-id="d29c9-223">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="d29c9-224">Jeśli pojedynczy składnik może korzystać z współbieżnie <xref:Microsoft.EntityFrameworkCore.DbContext> (na przykład za każdym razem, gdy użytkownik wybierze przycisk), nawet przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase> nie należy unikać problemów z jednoczesnymi operacjami EF.</span><span class="sxs-lookup"><span data-stu-id="d29c9-224">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="d29c9-225">W takim przypadku należy użyć innej <xref:Microsoft.EntityFrameworkCore.DbContext> dla każdej operacji logicznej EF.</span><span class="sxs-lookup"><span data-stu-id="d29c9-225">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="d29c9-226">Użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="d29c9-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="d29c9-227">Utwórz <xref:Microsoft.EntityFrameworkCore.DbContext> bezpośrednio przy użyciu <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> jako argument, który można pobrać z elementu "i" jest bezpieczny wątkowo.</span><span class="sxs-lookup"><span data-stu-id="d29c9-227">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="d29c9-228">Zarejestruj <xref:Microsoft.EntityFrameworkCore.DbContext> w kontenerze usługi z przejściowym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="d29c9-228">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="d29c9-229">Podczas rejestrowania kontekstu użyj <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-229">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d29c9-230"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Metoda rozszerzenia przyjmuje dwa opcjonalne parametry typu <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-230">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="d29c9-231">Aby skorzystać z tej metody, należy `contextLifetime` podać tylko parametr <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-231">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d29c9-232">`optionsLifetime`może zachować wartość domyślną <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-232">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="d29c9-233">Przejściowy <xref:Microsoft.EntityFrameworkCore.DbContext> można wstrzyknąć jako normalny (przy użyciu [`@inject`](xref:mvc/views/razor#inject) ) do składników, które nie wykonują równolegle wielu operacji EF.</span><span class="sxs-lookup"><span data-stu-id="d29c9-233">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="d29c9-234">Te, które mogą wykonywać wiele operacji EF jednocześnie, mogą zażądać oddzielnych <xref:Microsoft.EntityFrameworkCore.DbContext> obiektów dla każdej operacji równoległej przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="d29c9-234">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="d29c9-235">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d29c9-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="d29c9-236">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="d29c9-236">IDisposable guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
