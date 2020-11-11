---
title: 'ASP.NET Core :::no-loc(Blazor)::: wstrzykiwania zależności'
author: guardrex
description: 'Dowiedz się, jak :::no-loc(Blazor)::: aplikacje mogą wstrzyknąć usługi do składników programu.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 0cec9a1ea6f6df52103ab190c85518ddc42a573f
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507931"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="5b8e4-103">ASP.NET Core :::no-loc(Blazor)::: wstrzykiwania zależności</span><span class="sxs-lookup"><span data-stu-id="5b8e4-103">ASP.NET Core :::no-loc(Blazor)::: dependency injection</span></span>

<span data-ttu-id="5b8e4-104">Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="5b8e4-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="5b8e4-105">:::no-loc(Blazor)::: obsługuje [iniekcję zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5b8e4-105">:::no-loc(Blazor)::: supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5b8e4-106">Aplikacje mogą używać wbudowanych usług, wprowadzając je do składników programu.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="5b8e4-107">Aplikacje mogą także definiować i rejestrować niestandardowe usługi i udostępniać je w całej aplikacji za pomocą funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="5b8e4-108">DI jest techniką uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="5b8e4-109">Może to być przydatne w :::no-loc(Blazor)::: aplikacjach, aby:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-109">This can be useful in :::no-loc(Blazor)::: apps to:</span></span>

* <span data-ttu-id="5b8e4-110">Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanej jako *pojedyncze usługi.*</span><span class="sxs-lookup"><span data-stu-id="5b8e4-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="5b8e4-111">Oddziel składniki od klas konkretnych usług za pomocą abstrakcji odwołań.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="5b8e4-112">Rozważmy na przykład interfejs `IDataAccess` do uzyskiwania dostępu do danych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="5b8e4-113">Interfejs jest implementowany przez konkretną `DataAccess` klasę i zarejestrowany jako usługa w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="5b8e4-114">Gdy składnik używa elementu DI do odbierania `IDataAccess` implementacji, składnik nie jest połączony z konkretnym typem.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="5b8e4-115">Implementacja może zostać zamieniony, być może dla implementacji makiety w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="5b8e4-116">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="5b8e4-116">Default services</span></span>

<span data-ttu-id="5b8e4-117">Domyślne usługi są automatycznie dodawane do kolekcji usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="5b8e4-118">Usługa</span><span class="sxs-lookup"><span data-stu-id="5b8e4-118">Service</span></span> | <span data-ttu-id="5b8e4-119">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="5b8e4-119">Lifetime</span></span> | <span data-ttu-id="5b8e4-120">Opis</span><span class="sxs-lookup"><span data-stu-id="5b8e4-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="5b8e4-121">Zakresie</span><span class="sxs-lookup"><span data-stu-id="5b8e4-121">Scoped</span></span> | <span data-ttu-id="5b8e4-122">Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="5b8e4-123">Wystąpienie <xref:System.Net.Http.HttpClient> w :::no-loc(Blazor WebAssembly)::: aplikacji używa przeglądarki do obsługi ruchu HTTP w tle.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-123">The instance of <xref:System.Net.Http.HttpClient> in a :::no-loc(Blazor WebAssembly)::: app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="5b8e4-124">:::no-loc(Blazor Server)::: aplikacje nie domyślnie zawierają <xref:System.Net.Http.HttpClient> skonfigurowane jako usługa.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-124">:::no-loc(Blazor Server)::: apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="5b8e4-125">Udostępnianie <xref:System.Net.Http.HttpClient> :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-125">Provide an <xref:System.Net.Http.HttpClient> to a :::no-loc(Blazor Server)::: app.</span></span><br><br><span data-ttu-id="5b8e4-126">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="5b8e4-127"><xref:System.Net.Http.HttpClient>Jest zarejestrowany jako usługa o określonym zakresie, a nie pojedyncza.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="5b8e4-128">Aby uzyskać więcej informacji, zobacz sekcję [okres istnienia usługi](#service-lifetime) .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="5b8e4-129">Pojedyncze ( :::no-loc(Blazor WebAssembly)::: )</span><span class="sxs-lookup"><span data-stu-id="5b8e4-129">Singleton (:::no-loc(Blazor WebAssembly):::)</span></span><br><span data-ttu-id="5b8e4-130">W zakresie ( :::no-loc(Blazor Server)::: )</span><span class="sxs-lookup"><span data-stu-id="5b8e4-130">Scoped (:::no-loc(Blazor Server):::)</span></span> | <span data-ttu-id="5b8e4-131">Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="5b8e4-132">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="5b8e4-133">Pojedyncze ( :::no-loc(Blazor WebAssembly)::: )</span><span class="sxs-lookup"><span data-stu-id="5b8e4-133">Singleton (:::no-loc(Blazor WebAssembly):::)</span></span><br><span data-ttu-id="5b8e4-134">W zakresie ( :::no-loc(Blazor Server)::: )</span><span class="sxs-lookup"><span data-stu-id="5b8e4-134">Scoped (:::no-loc(Blazor Server):::)</span></span> | <span data-ttu-id="5b8e4-135">Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="5b8e4-136">Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="5b8e4-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="5b8e4-137">Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="5b8e4-138">W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="5b8e4-139">Dodawanie usług do aplikacji</span><span class="sxs-lookup"><span data-stu-id="5b8e4-139">Add services to an app</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="5b8e4-140">Skonfiguruj usługi dla kolekcji usług aplikacji w `Main` metodzie `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="5b8e4-141">W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="5b8e4-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="5b8e4-142">Po skompilowaniu hosta usługi można uzyskać dostęp z poziomu głównego DI zakresu przed renderowaniem wszystkich składników.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="5b8e4-143">Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-143">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="5b8e4-144">Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="5b8e4-145">W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład `:::no-loc(appsettings.json):::` ) do `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="5b8e4-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `:::no-loc(appsettings.json):::`) to `InitializeWeatherAsync`:</span></span>

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

### :::no-loc(Blazor Server):::

<span data-ttu-id="5b8e4-146">Po utworzeniu nowej aplikacji zapoznaj się z tą `Startup.ConfigureServices` metodą:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="5b8e4-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda jest przenoszona <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a, która jest listą obiektów deskryptora usługi ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="5b8e4-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="5b8e4-148">Usługi są dodawane w `ConfigureServices` metodzie przez dostarczenie deskryptorów usługi do kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="5b8e4-149">Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="5b8e4-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="5b8e4-150">Okres istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="5b8e4-150">Service lifetime</span></span>

<span data-ttu-id="5b8e4-151">Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="5b8e4-152">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="5b8e4-152">Lifetime</span></span> | <span data-ttu-id="5b8e4-153">Opis</span><span class="sxs-lookup"><span data-stu-id="5b8e4-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="5b8e4-154">:::no-loc(Blazor WebAssembly)::: aplikacje nie mają obecnie koncepcji DI Scopes.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-154">:::no-loc(Blazor WebAssembly)::: apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="5b8e4-155">`Scoped`-zarejestrowane usługi zachowują się jak `Singleton` usługi.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="5b8e4-156">Jednak :::no-loc(Blazor Server)::: model hostingu obsługuje `Scoped` okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-156">However, the :::no-loc(Blazor Server)::: hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="5b8e4-157">W :::no-loc(Blazor Server)::: aplikacjach do zakresu rejestracji usługi w zakresie jest *dołączany* zakres.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-157">In :::no-loc(Blazor Server)::: apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="5b8e4-158">Z tego powodu usługi w zakresie są preferowane dla usług, które powinny być objęte zakresem bieżącego użytkownika, nawet jeśli bieżącym celem jest uruchomienie po stronie klienta w przeglądarce w :::no-loc(Blazor WebAssembly)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser in a :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="5b8e4-159">DI tworzy *pojedyncze wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="5b8e4-160">Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="5b8e4-161">Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="5b8e4-162">System DI jest oparty na systemie DI w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="5b8e4-163">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="5b8e4-164">Żądanie usługi w składniku</span><span class="sxs-lookup"><span data-stu-id="5b8e4-164">Request a service in a component</span></span>

<span data-ttu-id="5b8e4-165">Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą dyrektywy [ \@ wstrzykiwania](xref:mvc/views/razor#inject) :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive.</span></span> <span data-ttu-id="5b8e4-166">[`@inject`](xref:mvc/views/razor#inject) ma dwa parametry:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-166">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="5b8e4-167">Typ: typ usługi do dodania.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-167">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="5b8e4-168">Property: Nazwa właściwości otrzymującej wstrzykiwaną usługę App Service.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-168">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="5b8e4-169">Właściwość nie wymaga ręcznego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="5b8e4-170">Kompilator tworzy właściwość.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-170">The compiler creates the property.</span></span>

<span data-ttu-id="5b8e4-171">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="5b8e4-172">Użyj wielu [`@inject`](xref:mvc/views/razor#inject) instrukcji, aby wstrzyknąć różne usługi.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-172">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="5b8e4-173">Poniższy przykład pokazuje, jak używać [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-173">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="5b8e4-174">Implementowanie usługi `Services.IDataAccess` jest wstrzykiwane do właściwości składnika `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="5b8e4-175">Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="5b8e4-176">Wewnętrznie wygenerowana Właściwość ( `DataRepository` ) używa [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-176">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="5b8e4-177">Zazwyczaj ten atrybut nie jest używany bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="5b8e4-178">Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-178">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="5b8e4-179">W składnikach pochodnych klasy bazowej [`@inject`](xref:mvc/views/razor#inject) dyrektywa nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-179">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="5b8e4-180"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Klasa bazowa jest wystarczająca:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-180">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="5b8e4-181">Korzystanie z usług DI w</span><span class="sxs-lookup"><span data-stu-id="5b8e4-181">Use DI in services</span></span>

<span data-ttu-id="5b8e4-182">Złożone usługi mogą wymagać dodatkowych usług.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-182">Complex services might require additional services.</span></span> <span data-ttu-id="5b8e4-183">W poprzednim przykładzie `DataAccess` może być wymagana <xref:System.Net.Http.HttpClient> Usługa domyślna.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-183">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="5b8e4-184">[`@inject`](xref:mvc/views/razor#inject) (lub [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut) nie jest dostępny do użytku w usługach.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-184">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="5b8e4-185">Zamiast tego należy użyć *iniekcji konstruktora* .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="5b8e4-186">Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="5b8e4-187">Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="5b8e4-188">W poniższym przykładzie Konstruktor odbiera <xref:System.Net.Http.HttpClient> przez di.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-188">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="5b8e4-189"><xref:System.Net.Http.HttpClient> jest domyślną usługą.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-189"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="5b8e4-190">Wymagania wstępne dotyczące iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-190">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="5b8e4-191">Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-191">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="5b8e4-192">Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-192">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="5b8e4-193">Odpowiedni Konstruktor musi być `public` .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-193">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="5b8e4-194">Musi istnieć jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-194">One applicable constructor must exist.</span></span> <span data-ttu-id="5b8e4-195">W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-195">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="5b8e4-196">Klasy składników podstawowych narzędzi do zarządzania DI zakresem</span><span class="sxs-lookup"><span data-stu-id="5b8e4-196">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="5b8e4-197">W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-197">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="5b8e4-198">Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-198">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="5b8e4-199">W :::no-loc(Blazor Server)::: aplikacjach zakres żądań jest stosowany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-199">In :::no-loc(Blazor Server)::: apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="5b8e4-200">W :::no-loc(Blazor WebAssembly)::: aplikacjach usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-200">In :::no-loc(Blazor WebAssembly)::: apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="5b8e4-201">Aby wykryć jednorazowe usługi przejściowe w aplikacji, zobacz sekcję [wykrywanie przejściowych](#detect-transient-disposables) elementów bezwzględnych.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-201">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="5b8e4-202">Zastosowanie tego typu w aplikacjach ogranicza okres istnienia usługi :::no-loc(Blazor)::: <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-202">An approach that limits a service lifetime in :::no-loc(Blazor)::: apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="5b8e4-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym typem pochodnym <xref:Microsoft.AspNetCore.Components.ComponentBase> , który tworzy zakres di odpowiadający okresowi istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="5b8e4-204">Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-204">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="5b8e4-205">Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-205">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="5b8e4-206">Może to być przydatne w przypadku usług, które:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-206">This can be useful for services that:</span></span>

* <span data-ttu-id="5b8e4-207">Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-207">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="5b8e4-208">Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-208">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="5b8e4-209">Dostępne są dwie wersje <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typu:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-209">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="5b8e4-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym, jednorazowym elementem podrzędnym <xref:Microsoft.AspNetCore.Components.ComponentBase> typu z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> właściwością chronioną typu <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="5b8e4-211">Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-211">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="5b8e4-212">Program DI Services wprowadzany do składnika przy użyciu [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu lub nie jest tworzony w zakresie składnika.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-212">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="5b8e4-213">Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> lub <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-213">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="5b8e4-214">Wszystkie usługi rozpoznane przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> dostawcy mają swoje zależności z tego samego zakresu.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-214">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="5b8e4-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> pochodzi z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i dodaje <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> Właściwość zwracającą wystąpienie `T` z dostawcy i zakresu.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="5b8e4-216">Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia, <xref:System.IServiceProvider> gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-216">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="5b8e4-217">Ta <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> Właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-217">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="5b8e4-218">Użycie Entity Framework Core (EF Core) DbContext z elementu DI</span><span class="sxs-lookup"><span data-stu-id="5b8e4-218">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="5b8e4-219">Aby uzyskać więcej informacji, zobacz <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="5b8e4-219">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="5b8e4-220">Wykrywanie przejściowych jednorazowych</span><span class="sxs-lookup"><span data-stu-id="5b8e4-220">Detect transient disposables</span></span>

<span data-ttu-id="5b8e4-221">Poniższe przykłady przedstawiają sposób wykrywania jednorazowych usług przejściowych w aplikacji, która powinna być używana <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-221">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="5b8e4-222">Aby uzyskać więcej informacji, zobacz [klasy składników podstawowych narzędzi, aby zarządzać sekcją di Scope](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="5b8e4-222">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="5b8e4-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="5b8e4-224">`TransientDisposable`Wykryto w poniższym przykładzie ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="5b8e4-224">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### :::no-loc(Blazor Server):::

<span data-ttu-id="5b8e4-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="5b8e4-226">`Program`:</span><span class="sxs-lookup"><span data-stu-id="5b8e4-226">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="5b8e4-227">`TransientDependency`Wykryto w poniższym przykładzie ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="5b8e4-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="5b8e4-228">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5b8e4-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="5b8e4-229">`IDisposable` Wskazówki dotyczące wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="5b8e4-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
