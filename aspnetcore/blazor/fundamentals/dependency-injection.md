---
title: ASP.NET Core Blazor wstrzykiwania zależności
author: guardrex
description: Dowiedz się, jak Blazor aplikacje mogą wstrzyknąć usługi do składników programu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: aefeac2f3a68a669b7c84cbeee2f6a4ec0621f6f
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109679"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="b632f-103">ASP.NET Core Blazor wstrzykiwania zależności</span><span class="sxs-lookup"><span data-stu-id="b632f-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="b632f-104">Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="b632f-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="b632f-105">[Iniekcja zależności (di)](xref:fundamentals/dependency-injection) to technika uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="b632f-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="b632f-106">Usługi zarejestrowane w ramach platformy można wstrzyknąć bezpośrednio do składników Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b632f-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="b632f-107">Blazor aplikacje definiują i rejestrują niestandardowe usługi i udostępniają je w całej aplikacji za pośrednictwem programu DI.</span><span class="sxs-lookup"><span data-stu-id="b632f-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="b632f-108">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="b632f-108">Default services</span></span>

<span data-ttu-id="b632f-109">Usługi przedstawione w poniższej tabeli są zwykle używane w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="b632f-109">The services shown in the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="b632f-110">Usługa</span><span class="sxs-lookup"><span data-stu-id="b632f-110">Service</span></span> | <span data-ttu-id="b632f-111">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="b632f-111">Lifetime</span></span> | <span data-ttu-id="b632f-112">Opis</span><span class="sxs-lookup"><span data-stu-id="b632f-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="b632f-113">Zakresie</span><span class="sxs-lookup"><span data-stu-id="b632f-113">Scoped</span></span> | <p><span data-ttu-id="b632f-114">Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="b632f-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="b632f-115">Wystąpienie <xref:System.Net.Http.HttpClient> w Blazor WebAssembly aplikacji używa przeglądarki do obsługi ruchu HTTP w tle.</span><span class="sxs-lookup"><span data-stu-id="b632f-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="b632f-116">Blazor Server aplikacje nie domyślnie zawierają <xref:System.Net.Http.HttpClient> skonfigurowane jako usługa.</span><span class="sxs-lookup"><span data-stu-id="b632f-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="b632f-117">Udostępnianie <xref:System.Net.Http.HttpClient> Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b632f-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="b632f-118">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="b632f-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="b632f-119"><xref:System.Net.Http.HttpClient>Jest zarejestrowany jako usługa o określonym zakresie, a nie pojedyncza.</span><span class="sxs-lookup"><span data-stu-id="b632f-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="b632f-120">Aby uzyskać więcej informacji, zobacz sekcję [okres istnienia usługi](#service-lifetime) .</span><span class="sxs-lookup"><span data-stu-id="b632f-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="b632f-121">**Blazor WebAssembly**: Singleton</span><span class="sxs-lookup"><span data-stu-id="b632f-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="b632f-122">**Blazor Server**: W zakresie</span><span class="sxs-lookup"><span data-stu-id="b632f-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="b632f-123">Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b632f-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="b632f-124">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="b632f-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="b632f-125">**Blazor WebAssembly**: Singleton</span><span class="sxs-lookup"><span data-stu-id="b632f-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="b632f-126">**Blazor Server**: W zakresie</span><span class="sxs-lookup"><span data-stu-id="b632f-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="b632f-127">Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b632f-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="b632f-128">Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="b632f-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="b632f-129">Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli.</span><span class="sxs-lookup"><span data-stu-id="b632f-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="b632f-130">W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="b632f-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="b632f-131">Dodawanie usług do aplikacji</span><span class="sxs-lookup"><span data-stu-id="b632f-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="b632f-132">Skonfiguruj usługi dla kolekcji usług aplikacji w `Program.Main` metodzie `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="b632f-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="b632f-133">W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="b632f-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="b632f-134">Po skompilowaniu hosta usługi są dostępne z poziomu korzenia DI przed renderowaniem wszystkich składników.</span><span class="sxs-lookup"><span data-stu-id="b632f-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="b632f-135">Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:</span><span class="sxs-lookup"><span data-stu-id="b632f-135">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="b632f-136">Host udostępnia centralne wystąpienie konfiguracji dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b632f-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="b632f-137">W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład `appsettings.json` ) do `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="b632f-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
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

::: zone-end

::: zone pivot="server"

<span data-ttu-id="b632f-138">Po utworzeniu nowej aplikacji zapoznaj się z tą `Startup.ConfigureServices` metodą w `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="b632f-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="b632f-139"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda jest przenoszona <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a, która jest listą obiektów [deskryptora usługi](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) .</span><span class="sxs-lookup"><span data-stu-id="b632f-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="b632f-140">Usługi są dodawane w `ConfigureServices` metodzie przez dostarczenie deskryptorów usługi do kolekcji usług.</span><span class="sxs-lookup"><span data-stu-id="b632f-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="b632f-141">Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="b632f-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="b632f-142">Okres istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="b632f-142">Service lifetime</span></span>

<span data-ttu-id="b632f-143">Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="b632f-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="b632f-144">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="b632f-144">Lifetime</span></span> | <span data-ttu-id="b632f-145">Opis</span><span class="sxs-lookup"><span data-stu-id="b632f-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="b632f-146">Blazor WebAssembly aplikacje nie mają obecnie koncepcji DI Scopes.</span><span class="sxs-lookup"><span data-stu-id="b632f-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="b632f-147">`Scoped`-zarejestrowane usługi zachowują się jak `Singleton` usługi.</span><span class="sxs-lookup"><span data-stu-id="b632f-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="b632f-148">Blazor ServerModel hostingu obsługuje `Scoped` okres istnienia w żądaniach HTTP, ale nie między SignalR komunikatami połączenia/obwodu między składnikami, które są ładowane na komputerze klienckim.</span><span class="sxs-lookup"><span data-stu-id="b632f-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SignalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="b632f-149">RazorCzęści strony lub MVC w aplikacji traktują usługi o określonym zakresie w normalny sposób i ponownie tworzy usługi na *każdym żądaniu HTTP* podczas nawigowania między stronami lub widokami albo ze strony lub widoku do składnika.</span><span class="sxs-lookup"><span data-stu-id="b632f-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="b632f-150">Usługi o określonym zakresie nie są odtworzone podczas nawigowania między składnikami na kliencie, gdzie komunikacja z serwerem odbywa się przez SignalR połączenie obwodu użytkownika, a nie za pośrednictwem żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="b632f-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="b632f-151">W następujących scenariuszach składników na kliencie usługi o określonym zakresie są odtworzone, ponieważ dla użytkownika zostanie utworzony nowy obwód:</span><span class="sxs-lookup"><span data-stu-id="b632f-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="b632f-152">Użytkownik zamknie okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b632f-152">The user closes the browser's window.</span></span> <span data-ttu-id="b632f-153">Użytkownik otworzy nowe okno i nawiguje z powrotem do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b632f-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="b632f-154">Użytkownik zamknie ostatnią kartę aplikacji w oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b632f-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="b632f-155">Użytkownik otworzy nową kartę i nawiguje z powrotem do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b632f-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="b632f-156">Użytkownik wybiera przycisk Załaduj ponownie/Odśwież w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="b632f-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="b632f-157">Aby uzyskać więcej informacji o zachowaniu stanu użytkownika w ramach usług w zakresie w Blazor Server aplikacjach, zobacz <xref:blazor/hosting-models?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="b632f-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="b632f-158">DI tworzy *pojedyncze wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="b632f-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="b632f-159">Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi.</span><span class="sxs-lookup"><span data-stu-id="b632f-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="b632f-160">Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi.</span><span class="sxs-lookup"><span data-stu-id="b632f-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="b632f-161">System DI jest oparty na systemie DI w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b632f-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="b632f-162">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b632f-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="b632f-163">Żądanie usługi w składniku</span><span class="sxs-lookup"><span data-stu-id="b632f-163">Request a service in a component</span></span>

<span data-ttu-id="b632f-164">Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywy, która ma dwa parametry:</span><span class="sxs-lookup"><span data-stu-id="b632f-164">After services are added to the service collection, inject the services into the components using the [`@inject`](xref:mvc/views/razor#inject) Razor directive, which has two parameters:</span></span>

* <span data-ttu-id="b632f-165">Typ: typ usługi do dodania.</span><span class="sxs-lookup"><span data-stu-id="b632f-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="b632f-166">Property: Nazwa właściwości otrzymującej wstrzykiwaną usługę App Service.</span><span class="sxs-lookup"><span data-stu-id="b632f-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="b632f-167">Właściwość nie wymaga ręcznego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="b632f-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="b632f-168">Kompilator tworzy właściwość.</span><span class="sxs-lookup"><span data-stu-id="b632f-168">The compiler creates the property.</span></span>

<span data-ttu-id="b632f-169">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b632f-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="b632f-170">Użyj wielu [`@inject`](xref:mvc/views/razor#inject) instrukcji, aby wstrzyknąć różne usługi.</span><span class="sxs-lookup"><span data-stu-id="b632f-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="b632f-171">Poniższy przykład pokazuje, jak używać [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="b632f-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="b632f-172">Implementowanie usługi `Services.IDataAccess` jest wstrzykiwane do właściwości składnika `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="b632f-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="b632f-173">Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:</span><span class="sxs-lookup"><span data-stu-id="b632f-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

<span data-ttu-id="b632f-174">Wewnętrznie wygenerowana Właściwość ( `DataRepository` ) używa [ `[Inject]` atrybutu](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="b632f-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span></span> <span data-ttu-id="b632f-175">Zazwyczaj ten atrybut nie jest używany bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="b632f-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="b632f-176">Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać [ `[Inject]` atrybut](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span><span class="sxs-lookup"><span data-stu-id="b632f-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="b632f-177">W składnikach pochodnych klasy bazowej [`@inject`](xref:mvc/views/razor#inject) dyrektywa nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="b632f-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="b632f-178"><xref:Microsoft.AspNetCore.Components.InjectAttribute>Klasa bazowa jest wystarczająca:</span><span class="sxs-lookup"><span data-stu-id="b632f-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="b632f-179">Korzystanie z usług DI w</span><span class="sxs-lookup"><span data-stu-id="b632f-179">Use DI in services</span></span>

<span data-ttu-id="b632f-180">Złożone usługi mogą wymagać dodatkowych usług.</span><span class="sxs-lookup"><span data-stu-id="b632f-180">Complex services might require additional services.</span></span> <span data-ttu-id="b632f-181">W poniższym przykładzie `DataAccess` wymaga <xref:System.Net.Http.HttpClient> usługi domyślnej.</span><span class="sxs-lookup"><span data-stu-id="b632f-181">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="b632f-182">[`@inject`](xref:mvc/views/razor#inject)(lub [ `[Inject]` atrybut](xref:Microsoft.AspNetCore.Components.InjectAttribute)) nie jest dostępny do użytku w usługach.</span><span class="sxs-lookup"><span data-stu-id="b632f-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute)) isn't available for use in services.</span></span> <span data-ttu-id="b632f-183">Zamiast tego należy użyć *iniekcji konstruktora* .</span><span class="sxs-lookup"><span data-stu-id="b632f-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="b632f-184">Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi.</span><span class="sxs-lookup"><span data-stu-id="b632f-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="b632f-185">Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="b632f-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="b632f-186">W poniższym przykładzie Konstruktor odbiera <xref:System.Net.Http.HttpClient> przez di.</span><span class="sxs-lookup"><span data-stu-id="b632f-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="b632f-187"><xref:System.Net.Http.HttpClient> jest domyślną usługą.</span><span class="sxs-lookup"><span data-stu-id="b632f-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="b632f-188">Wymagania wstępne dotyczące iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="b632f-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="b632f-189">Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI.</span><span class="sxs-lookup"><span data-stu-id="b632f-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="b632f-190">Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="b632f-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="b632f-191">Odpowiedni Konstruktor musi być `public` .</span><span class="sxs-lookup"><span data-stu-id="b632f-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="b632f-192">Musi istnieć jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="b632f-192">One applicable constructor must exist.</span></span> <span data-ttu-id="b632f-193">W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="b632f-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="b632f-194">Klasy składników podstawowych narzędzi do zarządzania DI zakresem</span><span class="sxs-lookup"><span data-stu-id="b632f-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="b632f-195">W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="b632f-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="b632f-196">Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI.</span><span class="sxs-lookup"><span data-stu-id="b632f-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="b632f-197">W Blazor Server aplikacjach zakres żądań jest stosowany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="b632f-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="b632f-198">W Blazor WebAssembly aplikacjach usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b632f-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="b632f-199">Aby wykryć jednorazowe usługi przejściowe w aplikacji, zobacz sekcję [wykrywanie przejściowych](#detect-transient-disposables) elementów bezwzględnych.</span><span class="sxs-lookup"><span data-stu-id="b632f-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="b632f-200">Zastosowanie tego typu w aplikacjach ogranicza okres istnienia usługi Blazor <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="b632f-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="b632f-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym typem pochodnym <xref:Microsoft.AspNetCore.Components.ComponentBase> , który tworzy zakres di odpowiadający okresowi istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="b632f-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="b632f-202">Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika.</span><span class="sxs-lookup"><span data-stu-id="b632f-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="b632f-203">Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="b632f-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="b632f-204">Może to być przydatne w przypadku usług, które:</span><span class="sxs-lookup"><span data-stu-id="b632f-204">This can be useful for services that:</span></span>

* <span data-ttu-id="b632f-205">Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="b632f-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="b632f-206">Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.</span><span class="sxs-lookup"><span data-stu-id="b632f-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="b632f-207">Dostępne są dwie wersje <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typu:</span><span class="sxs-lookup"><span data-stu-id="b632f-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="b632f-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym, jednorazowym elementem podrzędnym <xref:Microsoft.AspNetCore.Components.ComponentBase> typu z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> właściwością chronioną typu <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="b632f-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b632f-209">Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="b632f-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="b632f-210">Program DI Services wprowadzany do składnika przy użyciu [`@inject`](xref:mvc/views/razor#inject) [ `[Inject]` atrybutu](xref:Microsoft.AspNetCore.Components.InjectAttribute) lub nie jest tworzony w zakresie składnika.</span><span class="sxs-lookup"><span data-stu-id="b632f-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute) aren't created in the component's scope.</span></span> <span data-ttu-id="b632f-211">Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> lub <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="b632f-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="b632f-212">Wszystkie usługi rozpoznane przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> dostawcy mają swoje zależności z tego samego zakresu.</span><span class="sxs-lookup"><span data-stu-id="b632f-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

* <span data-ttu-id="b632f-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> pochodzi z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i dodaje <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> Właściwość zwracającą wystąpienie `T` z dostawcy i zakresu.</span><span class="sxs-lookup"><span data-stu-id="b632f-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="b632f-214">Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia, <xref:System.IServiceProvider> gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika.</span><span class="sxs-lookup"><span data-stu-id="b632f-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="b632f-215">Ta <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> Właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="b632f-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="b632f-216">Użycie Entity Framework Core (EF Core) DbContext z elementu DI</span><span class="sxs-lookup"><span data-stu-id="b632f-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="b632f-217">Aby uzyskać więcej informacji, zobacz <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="b632f-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="b632f-218">Wykrywanie przejściowych jednorazowych</span><span class="sxs-lookup"><span data-stu-id="b632f-218">Detect transient disposables</span></span>

<span data-ttu-id="b632f-219">Poniższe przykłady przedstawiają sposób wykrywania jednorazowych usług przejściowych w aplikacji, która powinna być używana <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="b632f-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="b632f-220">Aby uzyskać więcej informacji, zobacz [klasy składników podstawowych narzędzi, aby zarządzać sekcją di Scope](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="b632f-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="b632f-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="b632f-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

<span data-ttu-id="b632f-222">`TransientDisposable`Wykryto w poniższym przykładzie ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="b632f-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("#app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="b632f-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="b632f-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

<span data-ttu-id="b632f-224">Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> do `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="b632f-224">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="b632f-225">W `Program.CreateHostBuilder` programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="b632f-225">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .DetectIncorrectUsageOfTransients()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="b632f-226">`TransientDependency`Wykryto w poniższym przykładzie ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="b632f-226">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
    services.AddServerSideBlazor();
    services.AddSingleton<WeatherForecastService>();
    services.AddTransient<TransientDependency>();
    services.AddTransient<ITransitiveTransientDisposableDependency, 
        TransitiveTransientDisposableDependency>();
}

public class TransitiveTransientDisposableDependency 
    : ITransitiveTransientDisposableDependency, IDisposable
{
    public void Dispose() { }
}

public interface ITransitiveTransientDisposableDependency
{
}

public class TransientDependency
{
    private readonly ITransitiveTransientDisposableDependency 
        _transitiveTransientDisposableDependency;

    public TransientDependency(ITransitiveTransientDisposableDependency 
        transitiveTransientDisposableDependency)
    {
        _transitiveTransientDisposableDependency = 
            transitiveTransientDisposableDependency;
    }
}
```

::: zone-end

<span data-ttu-id="b632f-227">Aplikacja może rejestrować przejściowe jednorazowe, bez zgłaszania wyjątku.</span><span class="sxs-lookup"><span data-stu-id="b632f-227">The app can register transient disposables without throwing an exception.</span></span> <span data-ttu-id="b632f-228">Jednak próba rozpoznania przejściowych wyników jednorazowych w <xref:System.InvalidOperationException> , jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="b632f-228">However, attempting to resolve a transient disposable results in an <xref:System.InvalidOperationException>, as the following example shows.</span></span>

<span data-ttu-id="b632f-229">`Pages/TransientDisposable.razor`:</span><span class="sxs-lookup"><span data-stu-id="b632f-229">`Pages/TransientDisposable.razor`:</span></span>

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

<span data-ttu-id="b632f-230">Przejdź do `TransientDisposable` składnika w `/transient-disposable` i <xref:System.InvalidOperationException> jest generowany, gdy struktura próbuje utworzyć wystąpienie `TransientDisposable` :</span><span class="sxs-lookup"><span data-stu-id="b632f-230">Navigate to the `TransientDisposable` component at `/transient-disposable` and an <xref:System.InvalidOperationException> is thrown when the framework attempts to construct an instance of `TransientDisposable`:</span></span>

> <span data-ttu-id="b632f-231">System. InvalidOperationException: próba rozpoznania przejściowej jednorazowej usługi TransientDisposable w niewłaściwym zakresie.</span><span class="sxs-lookup"><span data-stu-id="b632f-231">System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span></span> <span data-ttu-id="b632f-232">Użyj \<T> klasy bazowej składnika "OwningComponentBase" dla usługi "t", którą próbujesz rozwiązać.</span><span class="sxs-lookup"><span data-stu-id="b632f-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b632f-233">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b632f-233">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="b632f-234">`IDisposable` Wskazówki dotyczące wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="b632f-234">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
