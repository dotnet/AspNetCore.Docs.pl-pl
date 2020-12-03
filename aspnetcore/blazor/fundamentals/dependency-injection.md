---
title: ASP.NET Core Blazor wstrzykiwania zależności
author: guardrex
description: Dowiedz się, jak Blazor aplikacje mogą wstrzyknąć usługi do składników programu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: c68deb5237754872e11bfd9c83275b9a3b147319
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556518"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="0ab4b-103">ASP.NET Core Blazor wstrzykiwania zależności</span><span class="sxs-lookup"><span data-stu-id="0ab4b-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="0ab4b-104">Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="0ab4b-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="0ab4b-105">Blazor obsługuje [iniekcję zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0ab4b-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0ab4b-106">Aplikacje mogą używać wbudowanych usług, wprowadzając je do składników programu.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="0ab4b-107">Aplikacje mogą także definiować i rejestrować niestandardowe usługi i udostępniać je w całej aplikacji za pomocą funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="0ab4b-108">DI jest techniką uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="0ab4b-109">Może to być przydatne w Blazor aplikacjach, aby:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="0ab4b-110">Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanej jako *pojedyncze usługi.*</span><span class="sxs-lookup"><span data-stu-id="0ab4b-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="0ab4b-111">Oddziel składniki od klas konkretnych usług za pomocą abstrakcji odwołań.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="0ab4b-112">Rozważmy na przykład interfejs `IDataAccess` do uzyskiwania dostępu do danych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="0ab4b-113">Interfejs jest implementowany przez konkretną `DataAccess` klasę i zarejestrowany jako usługa w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="0ab4b-114">Gdy składnik używa elementu DI do odbierania `IDataAccess` implementacji, składnik nie jest połączony z konkretnym typem.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="0ab4b-115">Implementacja może zostać zamieniony, być może dla implementacji makiety w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="0ab4b-116">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="0ab4b-116">Default services</span></span>

<span data-ttu-id="0ab4b-117">Domyślne usługi są automatycznie dodawane do kolekcji usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="0ab4b-118">Usługa</span><span class="sxs-lookup"><span data-stu-id="0ab4b-118">Service</span></span> | <span data-ttu-id="0ab4b-119">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="0ab4b-119">Lifetime</span></span> | <span data-ttu-id="0ab4b-120">Opis</span><span class="sxs-lookup"><span data-stu-id="0ab4b-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="0ab4b-121">Zakresie</span><span class="sxs-lookup"><span data-stu-id="0ab4b-121">Scoped</span></span> | <span data-ttu-id="0ab4b-122">Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="0ab4b-123">Wystąpienie <xref:System.Net.Http.HttpClient> w Blazor WebAssembly aplikacji używa przeglądarki do obsługi ruchu HTTP w tle.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="0ab4b-124">Blazor Server aplikacje nie domyślnie zawierają <xref:System.Net.Http.HttpClient> skonfigurowane jako usługa.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="0ab4b-125">Udostępnianie <xref:System.Net.Http.HttpClient> Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="0ab4b-126">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="0ab4b-127"><xref:System.Net.Http.HttpClient>Jest zarejestrowany jako usługa o określonym zakresie, a nie pojedyncza.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="0ab4b-128">Aby uzyskać więcej informacji, zobacz sekcję [okres istnienia usługi](#service-lifetime) .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="0ab4b-129">Pojedyncze ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="0ab4b-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="0ab4b-130">W zakresie ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="0ab4b-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="0ab4b-131">Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="0ab4b-132">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="0ab4b-133">Pojedyncze ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="0ab4b-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="0ab4b-134">W zakresie ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="0ab4b-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="0ab4b-135">Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="0ab4b-136">Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="0ab4b-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="0ab4b-137">Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="0ab4b-138">W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="0ab4b-139">Dodawanie usług do aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ab4b-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="0ab4b-140">Skonfiguruj usługi dla kolekcji usług aplikacji w `Main` metodzie `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="0ab4b-141">W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="0ab4b-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<IMyDependency, MyDependency>();

        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="0ab4b-142">Po skompilowaniu hosta usługi można uzyskać dostęp z poziomu głównego DI zakresu przed renderowaniem wszystkich składników.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="0ab4b-143">Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="0ab4b-144">Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="0ab4b-145">W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład `appsettings.json` ) do `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="0ab4b-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="0ab4b-146">Po utworzeniu nowej aplikacji zapoznaj się z tą `Startup.ConfigureServices` metodą:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="0ab4b-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda jest przenoszona <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a, która jest listą obiektów deskryptora usługi ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="0ab4b-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="0ab4b-148">Usługi są dodawane w `ConfigureServices` metodzie przez dostarczenie deskryptorów usługi do kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="0ab4b-149">Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="0ab4b-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="0ab4b-150">Okres istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="0ab4b-150">Service lifetime</span></span>

<span data-ttu-id="0ab4b-151">Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="0ab4b-152">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="0ab4b-152">Lifetime</span></span> | <span data-ttu-id="0ab4b-153">Opis</span><span class="sxs-lookup"><span data-stu-id="0ab4b-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="0ab4b-154">Blazor WebAssembly aplikacje nie mają obecnie koncepcji DI Scopes.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="0ab4b-155">`Scoped`-zarejestrowane usługi zachowują się jak `Singleton` usługi.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-155">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="0ab4b-156">Blazor ServerModel hostingu obsługuje `Scoped` okres istnienia między żądaniami HTTP, ale nie w przypadku komunikatów połączeń i obwodów usługi SingalR między składnikami, które są ładowane na komputerze klienckim.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-156">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="0ab4b-157">RazorCzęści strony lub MVC w aplikacji traktują usługi o określonym zakresie w normalny sposób i ponownie tworzy usługi na *każdym żądaniu HTTP* podczas nawigowania między stronami lub widokami albo ze strony lub widoku do składnika.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-157">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="0ab4b-158">Usługi o określonym zakresie nie są odtworzone podczas nawigowania między składnikami na kliencie, gdzie komunikacja z serwerem odbywa się przez SignalR połączenie obwodu użytkownika, a nie za pośrednictwem żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-158">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="0ab4b-159">W następujących scenariuszach składników na kliencie usługi o określonym zakresie są odtworzone, ponieważ dla użytkownika zostanie utworzony nowy obwód:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-159">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="0ab4b-160">Użytkownik zamknie okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-160">The user closes the browser's window.</span></span> <span data-ttu-id="0ab4b-161">Użytkownik otworzy nowe okno i nawiguje z powrotem do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-161">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="0ab4b-162">Użytkownik zamknie ostatnią kartę aplikacji w oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-162">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="0ab4b-163">Użytkownik otworzy nową kartę i nawiguje z powrotem do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-163">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="0ab4b-164">Użytkownik wybiera przycisk Załaduj ponownie/Odśwież w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-164">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="0ab4b-165">Aby uzyskać więcej informacji o zachowaniu stanu użytkownika w ramach usług w zakresie w Blazor Server aplikacjach, zobacz <xref:blazor/hosting-models?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-165">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="0ab4b-166">DI tworzy *pojedyncze wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-166">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="0ab4b-167">Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-167">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="0ab4b-168">Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-168">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="0ab4b-169">System DI jest oparty na systemie DI w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-169">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="0ab4b-170">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-170">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="0ab4b-171">Żądanie usługi w składniku</span><span class="sxs-lookup"><span data-stu-id="0ab4b-171">Request a service in a component</span></span>

<span data-ttu-id="0ab4b-172">Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą dyrektywy [ \@ wstrzykiwania](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-172">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="0ab4b-173">[`@inject`](xref:mvc/views/razor#inject) ma dwa parametry:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-173">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="0ab4b-174">Typ: typ usługi do dodania.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-174">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="0ab4b-175">Property: Nazwa właściwości otrzymującej wstrzykiwaną usługę App Service.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-175">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="0ab4b-176">Właściwość nie wymaga ręcznego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-176">The property doesn't require manual creation.</span></span> <span data-ttu-id="0ab4b-177">Kompilator tworzy właściwość.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-177">The compiler creates the property.</span></span>

<span data-ttu-id="0ab4b-178">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-178">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="0ab4b-179">Użyj wielu [`@inject`](xref:mvc/views/razor#inject) instrukcji, aby wstrzyknąć różne usługi.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-179">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="0ab4b-180">Poniższy przykład pokazuje, jak używać [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-180">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="0ab4b-181">Implementowanie usługi `Services.IDataAccess` jest wstrzykiwane do właściwości składnika `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-181">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="0ab4b-182">Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-182">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="0ab4b-183">Wewnętrznie wygenerowana Właściwość ( `DataRepository` ) używa [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-183">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="0ab4b-184">Zazwyczaj ten atrybut nie jest używany bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-184">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="0ab4b-185">Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-185">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="0ab4b-186">W składnikach pochodnych klasy bazowej [`@inject`](xref:mvc/views/razor#inject) dyrektywa nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-186">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="0ab4b-187"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Klasa bazowa jest wystarczająca:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-187">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="0ab4b-188">Korzystanie z usług DI w</span><span class="sxs-lookup"><span data-stu-id="0ab4b-188">Use DI in services</span></span>

<span data-ttu-id="0ab4b-189">Złożone usługi mogą wymagać dodatkowych usług.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-189">Complex services might require additional services.</span></span> <span data-ttu-id="0ab4b-190">W poprzednim przykładzie `DataAccess` może być wymagana <xref:System.Net.Http.HttpClient> Usługa domyślna.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-190">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="0ab4b-191">[`@inject`](xref:mvc/views/razor#inject) (lub [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut) nie jest dostępny do użytku w usługach.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-191">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="0ab4b-192">Zamiast tego należy użyć *iniekcji konstruktora* .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-192">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="0ab4b-193">Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-193">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="0ab4b-194">Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-194">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="0ab4b-195">W poniższym przykładzie Konstruktor odbiera <xref:System.Net.Http.HttpClient> przez di.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-195">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="0ab4b-196"><xref:System.Net.Http.HttpClient> jest domyślną usługą.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-196"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="0ab4b-197">Wymagania wstępne dotyczące iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-197">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="0ab4b-198">Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-198">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="0ab4b-199">Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-199">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="0ab4b-200">Odpowiedni Konstruktor musi być `public` .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-200">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="0ab4b-201">Musi istnieć jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-201">One applicable constructor must exist.</span></span> <span data-ttu-id="0ab4b-202">W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-202">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="0ab4b-203">Klasy składników podstawowych narzędzi do zarządzania DI zakresem</span><span class="sxs-lookup"><span data-stu-id="0ab4b-203">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="0ab4b-204">W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-204">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="0ab4b-205">Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-205">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="0ab4b-206">W Blazor Server aplikacjach zakres żądań jest stosowany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-206">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="0ab4b-207">W Blazor WebAssembly aplikacjach usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-207">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="0ab4b-208">Aby wykryć jednorazowe usługi przejściowe w aplikacji, zobacz sekcję [wykrywanie przejściowych](#detect-transient-disposables) elementów bezwzględnych.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-208">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="0ab4b-209">Zastosowanie tego typu w aplikacjach ogranicza okres istnienia usługi Blazor <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-209">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="0ab4b-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym typem pochodnym <xref:Microsoft.AspNetCore.Components.ComponentBase> , który tworzy zakres di odpowiadający okresowi istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="0ab4b-211">Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-211">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="0ab4b-212">Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-212">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="0ab4b-213">Może to być przydatne w przypadku usług, które:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-213">This can be useful for services that:</span></span>

* <span data-ttu-id="0ab4b-214">Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-214">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="0ab4b-215">Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-215">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="0ab4b-216">Dostępne są dwie wersje <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typu:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-216">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="0ab4b-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym, jednorazowym elementem podrzędnym <xref:Microsoft.AspNetCore.Components.ComponentBase> typu z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> właściwością chronioną typu <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0ab4b-218">Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-218">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="0ab4b-219">Program DI Services wprowadzany do składnika przy użyciu [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu lub nie jest tworzony w zakresie składnika.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-219">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="0ab4b-220">Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> lub <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-220">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="0ab4b-221">Wszystkie usługi rozpoznane przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> dostawcy mają swoje zależności z tego samego zakresu.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-221">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="0ab4b-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> pochodzi z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i dodaje <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> Właściwość zwracającą wystąpienie `T` z dostawcy i zakresu.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="0ab4b-223">Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia, <xref:System.IServiceProvider> gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-223">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="0ab4b-224">Ta <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> Właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-224">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="0ab4b-225">Użycie Entity Framework Core (EF Core) DbContext z elementu DI</span><span class="sxs-lookup"><span data-stu-id="0ab4b-225">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="0ab4b-226">Aby uzyskać więcej informacji, zobacz <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="0ab4b-226">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="0ab4b-227">Wykrywanie przejściowych jednorazowych</span><span class="sxs-lookup"><span data-stu-id="0ab4b-227">Detect transient disposables</span></span>

<span data-ttu-id="0ab4b-228">Poniższe przykłady przedstawiają sposób wykrywania jednorazowych usług przejściowych w aplikacji, która powinna być używana <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-228">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="0ab4b-229">Aby uzyskać więcej informacji, zobacz [klasy składników podstawowych narzędzi, aby zarządzać sekcją di Scope](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="0ab4b-229">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="0ab4b-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="0ab4b-231">`TransientDisposable`Wykryto w poniższym przykładzie ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="0ab4b-231">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### Blazor Server

<span data-ttu-id="0ab4b-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="0ab4b-233">`Program`:</span><span class="sxs-lookup"><span data-stu-id="0ab4b-233">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="0ab4b-234">`TransientDependency`Wykryto w poniższym przykładzie ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="0ab4b-234">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="0ab4b-235">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0ab4b-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="0ab4b-236">`IDisposable` Wskazówki dotyczące wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="0ab4b-236">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
