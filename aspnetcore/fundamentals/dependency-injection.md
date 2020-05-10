---
title: Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)
author: rick-anderson
description: Dowiedz się, w jaki sposób ASP.NET Core implementuje iniekcję zależności i jak z niej korzystać.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3e31be02f21f8c28c1d98d47d9a744b3a8502253
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003180"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="8313b-103">Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="8313b-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="8313b-104">[Steve Kowalski](https://ardalis.com/) i [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="8313b-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8313b-105">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="8313b-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="8313b-106">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8313b-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="8313b-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8313b-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="8313b-108">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="8313b-108">Overview of dependency injection</span></span>

<span data-ttu-id="8313b-109">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="8313b-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="8313b-110">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8313b-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="8313b-111">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="8313b-112">`MyDependency` Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="8313b-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="8313b-113">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8313b-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="8313b-114">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="8313b-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="8313b-115">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="8313b-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="8313b-116">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="8313b-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="8313b-117">W dużym projekcie z wieloma klasami `MyDependency`, w zależności od tego, kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="8313b-118">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="8313b-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="8313b-119">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="8313b-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="8313b-120">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="8313b-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="8313b-121">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="8313b-122">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="8313b-123">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="8313b-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="8313b-124">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="8313b-125">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="8313b-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="8313b-126">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="8313b-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="8313b-127">W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8313b-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="8313b-128">Ten interfejs jest implementowany przez konkretny typ `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="8313b-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="8313b-129">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="8313b-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="8313b-130">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="8313b-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="8313b-131">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="8313b-132">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="8313b-133">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="8313b-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="8313b-134">`IMyDependency`i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="8313b-135">`IMyDependency`jest zarejestrowany w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8313b-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8313b-136">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="8313b-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="8313b-137">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="8313b-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="8313b-138">W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana w konkretnym typie `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8313b-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="8313b-139">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="8313b-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="8313b-140">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="8313b-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="8313b-141">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="8313b-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="8313b-142">Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC.</span><span class="sxs-lookup"><span data-stu-id="8313b-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="8313b-143">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="8313b-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="8313b-144">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="8313b-145">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string`,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="8313b-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="8313b-146">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="8313b-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="8313b-147">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="8313b-148">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="8313b-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="8313b-149">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="8313b-149">Services injected into Startup</span></span>

<span data-ttu-id="8313b-150">Tylko następujące typy usług można wstrzyknąć do konstruktora, `Startup` gdy jest używany host generyczny (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="8313b-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8313b-151">Usługi można wstrzyknąć do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="8313b-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="8313b-152">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8313b-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8313b-153">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="8313b-153">Framework-provided services</span></span>

<span data-ttu-id="8313b-154">`Startup.ConfigureServices` Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8313b-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="8313b-155">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8313b-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="8313b-156">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="8313b-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="8313b-157">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="8313b-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="8313b-158">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="8313b-158">Service Type</span></span> | <span data-ttu-id="8313b-159">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="8313b-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="8313b-160">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="8313b-161">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="8313b-162">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="8313b-163">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="8313b-164">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="8313b-165">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="8313b-166">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="8313b-167">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="8313b-168">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="8313b-169">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="8313b-170">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="8313b-171">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="8313b-172">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="8313b-173">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="8313b-174">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="8313b-174">Register additional services with extension methods</span></span>

<span data-ttu-id="8313b-175">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać `Add{SERVICE_NAME}` pojedynczej metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="8313b-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="8313b-176">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="8313b-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="8313b-177">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="8313b-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="8313b-178">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="8313b-178">Service lifetimes</span></span>

<span data-ttu-id="8313b-179">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="8313b-180">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="8313b-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="8313b-181">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-181">Transient</span></span>

<span data-ttu-id="8313b-182">Przejściowe usługi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>okresu istnienia są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="8313b-183">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="8313b-184">Zakresie</span><span class="sxs-lookup"><span data-stu-id="8313b-184">Scoped</span></span>

<span data-ttu-id="8313b-185">Usługi okresu istnienia w<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>zakresie () są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="8313b-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="8313b-186">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` metody `InvokeAsync` lub.</span><span class="sxs-lookup"><span data-stu-id="8313b-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="8313b-187">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="8313b-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="8313b-188">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="8313b-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="8313b-189">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-189">Singleton</span></span>

<span data-ttu-id="8313b-190">Pojedyncze usługi okresu istnienia (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="8313b-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="8313b-191">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8313b-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="8313b-192">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="8313b-193">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="8313b-194">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="8313b-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="8313b-195">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="8313b-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="8313b-196">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="8313b-196">Service registration methods</span></span>

<span data-ttu-id="8313b-197">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="8313b-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="8313b-198">Metoda</span><span class="sxs-lookup"><span data-stu-id="8313b-198">Method</span></span> | <span data-ttu-id="8313b-199">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="8313b-199">Automatic</span></span><br><span data-ttu-id="8313b-200">object</span><span class="sxs-lookup"><span data-stu-id="8313b-200">object</span></span><br><span data-ttu-id="8313b-201">myśl</span><span class="sxs-lookup"><span data-stu-id="8313b-201">disposal</span></span> | <span data-ttu-id="8313b-202">Wiele</span><span class="sxs-lookup"><span data-stu-id="8313b-202">Multiple</span></span><br><span data-ttu-id="8313b-203">implementacje</span><span class="sxs-lookup"><span data-stu-id="8313b-203">implementations</span></span> | <span data-ttu-id="8313b-204">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="8313b-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="8313b-205">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8313b-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="8313b-206">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-206">Yes</span></span> | <span data-ttu-id="8313b-207">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-207">Yes</span></span> | <span data-ttu-id="8313b-208">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="8313b-209">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="8313b-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="8313b-210">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-210">Yes</span></span> | <span data-ttu-id="8313b-211">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-211">Yes</span></span> | <span data-ttu-id="8313b-212">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="8313b-213">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8313b-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="8313b-214">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-214">Yes</span></span> | <span data-ttu-id="8313b-215">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-215">No</span></span> | <span data-ttu-id="8313b-216">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="8313b-217">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="8313b-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="8313b-218">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-218">No</span></span> | <span data-ttu-id="8313b-219">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-219">Yes</span></span> | <span data-ttu-id="8313b-220">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="8313b-221">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="8313b-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="8313b-222">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-222">No</span></span> | <span data-ttu-id="8313b-223">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-223">No</span></span> | <span data-ttu-id="8313b-224">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-224">Yes</span></span> |

<span data-ttu-id="8313b-225">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="8313b-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="8313b-226">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="8313b-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="8313b-227">`TryAdd{LIFETIME}`Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="8313b-228">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8313b-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="8313b-229">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="8313b-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="8313b-230">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="8313b-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="8313b-231">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="8313b-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="8313b-232">Wiele usług jest rozpoznawanych `IEnumerable<{SERVICE}>`za pośrednictwem.</span><span class="sxs-lookup"><span data-stu-id="8313b-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="8313b-233">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="8313b-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="8313b-234">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="8313b-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="8313b-235">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="8313b-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="8313b-236">Drugi wiersz rejestruje `MyDep` `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="8313b-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="8313b-237">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja: `MyDep`</span><span class="sxs-lookup"><span data-stu-id="8313b-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="8313b-238">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="8313b-238">Constructor injection behavior</span></span>

<span data-ttu-id="8313b-239">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="8313b-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="8313b-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="8313b-241">`ActivatorUtilities`jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="8313b-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="8313b-242">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="8313b-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="8313b-243">Gdy usługi są rozwiązane `IServiceProvider` przez `ActivatorUtilities`lub, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="8313b-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="8313b-244">Gdy usługi są rozwiązane `ActivatorUtilities`przez, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="8313b-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="8313b-245">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="8313b-246">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8313b-246">Entity Framework contexts</span></span>

<span data-ttu-id="8313b-247">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="8313b-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="8313b-248">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez Przeciążenie [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) Overload podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8313b-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="8313b-249">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="8313b-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="8313b-250">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="8313b-250">Lifetime and registration options</span></span>

<span data-ttu-id="8313b-251">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje `OperationId`z unikatowym identyfikatorem.</span><span class="sxs-lookup"><span data-stu-id="8313b-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="8313b-252">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="8313b-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="8313b-253">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="8313b-254">`Operation` Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="8313b-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="8313b-255">`OperationService` Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="8313b-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="8313b-256">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="8313b-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="8313b-257">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` `IOperationTransient` usługa różni się od `OperationId` `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8313b-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="8313b-258">`OperationService`odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="8313b-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="8313b-259">Nowe wystąpienie daje inną `OperationId`wartość.</span><span class="sxs-lookup"><span data-stu-id="8313b-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="8313b-260">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="8313b-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="8313b-261">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="8313b-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="8313b-262">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="8313b-263">W `Startup.ConfigureServices`programie każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="8313b-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="8313b-264">`IOperationSingletonInstance` Usługa używa określonego wystąpienia o ZNANYm identyfikatorze `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8313b-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="8313b-265">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="8313b-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="8313b-266">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="8313b-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="8313b-267">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="8313b-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="8313b-268">Następnie na stronie zostaną wyświetlone wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="8313b-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="8313b-269">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="8313b-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="8313b-270">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="8313b-270">**First request:**</span></span>

<span data-ttu-id="8313b-271">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="8313b-271">Controller operations:</span></span>

<span data-ttu-id="8313b-272">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="8313b-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="8313b-273">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8313b-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8313b-274">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-275">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-276">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="8313b-276">`OperationService` operations:</span></span>

<span data-ttu-id="8313b-277">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="8313b-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="8313b-278">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8313b-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8313b-279">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-280">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-281">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="8313b-281">**Second request:**</span></span>

<span data-ttu-id="8313b-282">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="8313b-282">Controller operations:</span></span>

<span data-ttu-id="8313b-283">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="8313b-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="8313b-284">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8313b-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8313b-285">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-286">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-287">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="8313b-287">`OperationService` operations:</span></span>

<span data-ttu-id="8313b-288">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="8313b-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="8313b-289">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8313b-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8313b-290">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-291">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-292">Zauważ, że wartości `OperationId` różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="8313b-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="8313b-293">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="8313b-293">*Transient* objects are always different.</span></span> <span data-ttu-id="8313b-294">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="8313b-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="8313b-295">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="8313b-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="8313b-296">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="8313b-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="8313b-297">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, `Operation` czy wystąpienie jest dostarczone `Startup.ConfigureServices`w.</span><span class="sxs-lookup"><span data-stu-id="8313b-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="8313b-298">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="8313b-298">Call services from main</span></span>

<span data-ttu-id="8313b-299">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="8313b-300">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="8313b-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="8313b-301">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="8313b-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="8313b-302">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="8313b-302">Scope validation</span></span>

<span data-ttu-id="8313b-303">Gdy aplikacja jest uruchomiona w środowisku deweloperskim i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do skompilowania hosta, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="8313b-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8313b-304">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="8313b-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="8313b-305">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="8313b-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="8313b-306">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="8313b-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="8313b-307">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8313b-308">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="8313b-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8313b-309">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="8313b-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="8313b-310">Sprawdzanie poprawności zakresów usług przechwytuje te `BuildServiceProvider` sytuacje, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="8313b-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8313b-311">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="8313b-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="8313b-312">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="8313b-312">Request Services</span></span>

<span data-ttu-id="8313b-313">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="8313b-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="8313b-314">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="8313b-315">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices`, nie. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="8313b-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="8313b-316">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="8313b-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="8313b-317">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól platformie na wstrzyknięcie zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="8313b-318">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="8313b-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="8313b-319">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp `RequestServices` do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8313b-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="8313b-320">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="8313b-320">Design services for dependency injection</span></span>

<span data-ttu-id="8313b-321">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="8313b-321">Best practices are to:</span></span>

* <span data-ttu-id="8313b-322">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="8313b-323">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="8313b-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="8313b-324">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="8313b-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="8313b-325">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="8313b-326">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="8313b-327">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="8313b-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="8313b-328">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="8313b-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="8313b-329">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="8313b-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="8313b-330">Należy pamiętać, że klasy Razor Pages modelu strony i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8313b-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="8313b-331">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="8313b-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="8313b-332">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="8313b-332">Disposal of services</span></span>

<span data-ttu-id="8313b-333">Kontener wywołuje <xref:System.IDisposable.Dispose*> typy, które <xref:System.IDisposable> tworzy.</span><span class="sxs-lookup"><span data-stu-id="8313b-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="8313b-334">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="8313b-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="8313b-335">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="8313b-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="8313b-336">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8313b-336">In the following example:</span></span>

* <span data-ttu-id="8313b-337">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="8313b-338">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="8313b-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="8313b-339">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="8313b-340">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="8313b-341">Aby zapoznać się z omówieniem opcji usuwania usług, zobacz [cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="8313b-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="8313b-342">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="8313b-342">Default service container replacement</span></span>

<span data-ttu-id="8313b-343">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="8313b-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="8313b-344">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="8313b-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="8313b-345">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="8313b-345">Property injection</span></span>
* <span data-ttu-id="8313b-346">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="8313b-346">Injection based on name</span></span>
* <span data-ttu-id="8313b-347">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="8313b-347">Child containers</span></span>
* <span data-ttu-id="8313b-348">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="8313b-348">Custom lifetime management</span></span>
* <span data-ttu-id="8313b-349">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="8313b-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="8313b-350">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="8313b-350">Convention-based registration</span></span>

<span data-ttu-id="8313b-351">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="8313b-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="8313b-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="8313b-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="8313b-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="8313b-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="8313b-354">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="8313b-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="8313b-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="8313b-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="8313b-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="8313b-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="8313b-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="8313b-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="8313b-358">Unity</span><span class="sxs-lookup"><span data-stu-id="8313b-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="8313b-359">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="8313b-359">Thread safety</span></span>

<span data-ttu-id="8313b-360">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="8313b-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="8313b-361">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="8313b-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="8313b-362">Metoda fabryki pojedynczej usługi, taka jak drugi argument dla [\<addsingleton TService> (IServiceCollection, Func\<IServiceProvider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="8313b-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="8313b-363">Podobnie jak w przypadku`static`konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="8313b-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8313b-364">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="8313b-364">Recommendations</span></span>

* <span data-ttu-id="8313b-365">`async/await`Rozpoznawanie `Task` usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8313b-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="8313b-366">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="8313b-367">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="8313b-368">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="8313b-369">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8313b-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8313b-370">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="8313b-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="8313b-371">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="8313b-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="8313b-372">Należy unikać statycznego dostępu do usług (na przykład statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="8313b-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="8313b-373">Unikaj używania *wzorca lokalizatora usługi*.</span><span class="sxs-lookup"><span data-stu-id="8313b-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="8313b-374">Na przykład nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="8313b-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="8313b-375">**Prawidłowy**</span><span class="sxs-lookup"><span data-stu-id="8313b-375">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="8313b-376">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="8313b-376">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="8313b-377">Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8313b-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="8313b-378">Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="8313b-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="8313b-379">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="8313b-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="8313b-380">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="8313b-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="8313b-381">Wyjątki są rzadko&mdash;w większości specjalnych przypadków w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="8313b-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="8313b-382">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="8313b-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="8313b-383">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="8313b-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="8313b-384">Zalecane wzorce dla wielu dzierżawców w programie DI</span><span class="sxs-lookup"><span data-stu-id="8313b-384">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="8313b-385">[Podstawowe funkcje sadu](https://github.com/OrchardCMS/OrchardCore) zapewniają wiele dzierżawców.</span><span class="sxs-lookup"><span data-stu-id="8313b-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="8313b-386">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją podstawową programu sadu](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="8313b-386">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="8313b-387">Zapoznaj się z przykładowymi aplikacjami, https://github.com/OrchardCMS/OrchardCore.Samples aby poznać Przykłady sposobu tworzenia aplikacji modularnych i wielodostępnych przy użyciu samej platformy sadu Core bez żadnej z określonych funkcji usługi CMS.</span><span class="sxs-lookup"><span data-stu-id="8313b-387">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8313b-388">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8313b-388">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="8313b-389">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="8313b-389">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="8313b-390">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="8313b-390">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="8313b-391">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="8313b-391">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="8313b-392">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="8313b-392">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8313b-393">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="8313b-393">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="8313b-394">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8313b-394">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="8313b-395">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8313b-395">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="8313b-396">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="8313b-396">Overview of dependency injection</span></span>

<span data-ttu-id="8313b-397">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="8313b-397">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="8313b-398">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8313b-398">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="8313b-399">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-399">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="8313b-400">`MyDependency` Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="8313b-400">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="8313b-401">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8313b-401">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="8313b-402">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="8313b-402">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="8313b-403">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="8313b-403">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="8313b-404">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="8313b-404">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="8313b-405">W dużym projekcie z wieloma klasami `MyDependency`, w zależności od tego, kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-405">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="8313b-406">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="8313b-406">This implementation is difficult to unit test.</span></span> <span data-ttu-id="8313b-407">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="8313b-407">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="8313b-408">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="8313b-408">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="8313b-409">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-409">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="8313b-410">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-410">Registration of the dependency in a service container.</span></span> <span data-ttu-id="8313b-411">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="8313b-411">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="8313b-412">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-412">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="8313b-413">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="8313b-413">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="8313b-414">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="8313b-414">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="8313b-415">W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8313b-415">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="8313b-416">Ten interfejs jest implementowany przez konkretny typ `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="8313b-416">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="8313b-417">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="8313b-417">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="8313b-418">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="8313b-418">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="8313b-419">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-419">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="8313b-420">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-420">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="8313b-421">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="8313b-421">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="8313b-422">`IMyDependency`i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-422">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="8313b-423">`IMyDependency`jest zarejestrowany w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8313b-423">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8313b-424">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="8313b-424">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="8313b-425">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="8313b-425">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="8313b-426">W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana w konkretnym typie `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8313b-426">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="8313b-427">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="8313b-427">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="8313b-428">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="8313b-428">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="8313b-429">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="8313b-429">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="8313b-430">Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC.</span><span class="sxs-lookup"><span data-stu-id="8313b-430">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="8313b-431">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="8313b-431">We recommended that apps follow this convention.</span></span> <span data-ttu-id="8313b-432">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-432">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="8313b-433">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string`,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="8313b-433">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="8313b-434">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="8313b-434">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="8313b-435">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-435">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="8313b-436">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="8313b-436">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="8313b-437">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="8313b-437">Services injected into Startup</span></span>

<span data-ttu-id="8313b-438">Tylko następujące typy usług można wstrzyknąć do konstruktora, `Startup` gdy jest używany host generyczny (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="8313b-438">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8313b-439">Usługi można wstrzyknąć do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="8313b-439">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="8313b-440">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8313b-440">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8313b-441">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="8313b-441">Framework-provided services</span></span>

<span data-ttu-id="8313b-442">`Startup.ConfigureServices` Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="8313b-442">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="8313b-443">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8313b-443">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="8313b-444">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="8313b-444">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="8313b-445">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="8313b-445">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="8313b-446">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="8313b-446">Service Type</span></span> | <span data-ttu-id="8313b-447">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="8313b-447">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="8313b-448">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="8313b-449">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="8313b-450">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-450">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="8313b-451">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-451">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="8313b-452">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-452">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="8313b-453">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-453">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="8313b-454">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="8313b-455">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-455">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="8313b-456">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-456">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="8313b-457">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-457">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="8313b-458">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-458">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="8313b-459">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-459">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="8313b-460">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-460">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="8313b-461">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-461">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="8313b-462">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="8313b-462">Register additional services with extension methods</span></span>

<span data-ttu-id="8313b-463">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać `Add{SERVICE_NAME}` pojedynczej metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="8313b-463">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="8313b-464">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="8313b-464">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="8313b-465">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="8313b-465">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="8313b-466">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="8313b-466">Service lifetimes</span></span>

<span data-ttu-id="8313b-467">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-467">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="8313b-468">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="8313b-468">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="8313b-469">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="8313b-469">Transient</span></span>

<span data-ttu-id="8313b-470">Przejściowe usługi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>okresu istnienia są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-470">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="8313b-471">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-471">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="8313b-472">Zakresie</span><span class="sxs-lookup"><span data-stu-id="8313b-472">Scoped</span></span>

<span data-ttu-id="8313b-473">Usługi okresu istnienia w<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>zakresie () są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="8313b-473">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="8313b-474">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` metody `InvokeAsync` lub.</span><span class="sxs-lookup"><span data-stu-id="8313b-474">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="8313b-475">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="8313b-475">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="8313b-476">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="8313b-476">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="8313b-477">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="8313b-477">Singleton</span></span>

<span data-ttu-id="8313b-478">Pojedyncze usługi okresu istnienia (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="8313b-478">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="8313b-479">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8313b-479">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="8313b-480">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-480">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="8313b-481">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-481">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="8313b-482">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="8313b-482">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="8313b-483">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="8313b-483">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="8313b-484">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="8313b-484">Service registration methods</span></span>

<span data-ttu-id="8313b-485">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="8313b-485">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="8313b-486">Metoda</span><span class="sxs-lookup"><span data-stu-id="8313b-486">Method</span></span> | <span data-ttu-id="8313b-487">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="8313b-487">Automatic</span></span><br><span data-ttu-id="8313b-488">object</span><span class="sxs-lookup"><span data-stu-id="8313b-488">object</span></span><br><span data-ttu-id="8313b-489">myśl</span><span class="sxs-lookup"><span data-stu-id="8313b-489">disposal</span></span> | <span data-ttu-id="8313b-490">Wiele</span><span class="sxs-lookup"><span data-stu-id="8313b-490">Multiple</span></span><br><span data-ttu-id="8313b-491">implementacje</span><span class="sxs-lookup"><span data-stu-id="8313b-491">implementations</span></span> | <span data-ttu-id="8313b-492">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="8313b-492">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="8313b-493">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8313b-493">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="8313b-494">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-494">Yes</span></span> | <span data-ttu-id="8313b-495">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-495">Yes</span></span> | <span data-ttu-id="8313b-496">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-496">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="8313b-497">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="8313b-497">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="8313b-498">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-498">Yes</span></span> | <span data-ttu-id="8313b-499">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-499">Yes</span></span> | <span data-ttu-id="8313b-500">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-500">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="8313b-501">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8313b-501">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="8313b-502">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-502">Yes</span></span> | <span data-ttu-id="8313b-503">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-503">No</span></span> | <span data-ttu-id="8313b-504">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-504">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="8313b-505">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="8313b-505">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="8313b-506">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-506">No</span></span> | <span data-ttu-id="8313b-507">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-507">Yes</span></span> | <span data-ttu-id="8313b-508">Tak</span><span class="sxs-lookup"><span data-stu-id="8313b-508">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="8313b-509">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="8313b-509">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="8313b-510">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-510">No</span></span> | <span data-ttu-id="8313b-511">Nie</span><span class="sxs-lookup"><span data-stu-id="8313b-511">No</span></span> | <span data-ttu-id="8313b-512">Yes</span><span class="sxs-lookup"><span data-stu-id="8313b-512">Yes</span></span> |

<span data-ttu-id="8313b-513">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="8313b-513">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="8313b-514">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="8313b-514">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="8313b-515">`TryAdd{LIFETIME}`Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-515">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="8313b-516">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="8313b-516">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="8313b-517">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="8313b-517">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="8313b-518">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="8313b-518">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="8313b-519">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="8313b-519">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="8313b-520">Wiele usług jest rozpoznawanych `IEnumerable<{SERVICE}>`za pośrednictwem.</span><span class="sxs-lookup"><span data-stu-id="8313b-520">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="8313b-521">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="8313b-521">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="8313b-522">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="8313b-522">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="8313b-523">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="8313b-523">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="8313b-524">Drugi wiersz rejestruje `MyDep` `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="8313b-524">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="8313b-525">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja: `MyDep`</span><span class="sxs-lookup"><span data-stu-id="8313b-525">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="8313b-526">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="8313b-526">Constructor injection behavior</span></span>

<span data-ttu-id="8313b-527">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="8313b-527">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="8313b-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="8313b-529">`ActivatorUtilities`jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="8313b-529">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="8313b-530">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="8313b-530">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="8313b-531">Gdy usługi są rozwiązane `IServiceProvider` przez `ActivatorUtilities`lub, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="8313b-531">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="8313b-532">Gdy usługi są rozwiązane `ActivatorUtilities`przez, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="8313b-532">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="8313b-533">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-533">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="8313b-534">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8313b-534">Entity Framework contexts</span></span>

<span data-ttu-id="8313b-535">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="8313b-535">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="8313b-536">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez Przeciążenie [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) Overload podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8313b-536">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="8313b-537">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="8313b-537">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="8313b-538">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="8313b-538">Lifetime and registration options</span></span>

<span data-ttu-id="8313b-539">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje `OperationId`z unikatowym identyfikatorem.</span><span class="sxs-lookup"><span data-stu-id="8313b-539">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="8313b-540">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="8313b-540">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="8313b-541">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="8313b-541">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="8313b-542">`Operation` Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="8313b-542">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="8313b-543">`OperationService` Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="8313b-543">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="8313b-544">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="8313b-544">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="8313b-545">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` `IOperationTransient` usługa różni się od `OperationId` `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="8313b-545">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="8313b-546">`OperationService`odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="8313b-546">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="8313b-547">Nowe wystąpienie daje inną `OperationId`wartość.</span><span class="sxs-lookup"><span data-stu-id="8313b-547">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="8313b-548">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="8313b-548">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="8313b-549">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="8313b-549">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="8313b-550">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-550">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="8313b-551">W `Startup.ConfigureServices`programie każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="8313b-551">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="8313b-552">`IOperationSingletonInstance` Usługa używa określonego wystąpienia o ZNANYm identyfikatorze `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8313b-552">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="8313b-553">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="8313b-553">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="8313b-554">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="8313b-554">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="8313b-555">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="8313b-555">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="8313b-556">Następnie na stronie zostaną wyświetlone wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="8313b-556">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="8313b-557">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="8313b-557">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="8313b-558">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="8313b-558">**First request:**</span></span>

<span data-ttu-id="8313b-559">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="8313b-559">Controller operations:</span></span>

<span data-ttu-id="8313b-560">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="8313b-560">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="8313b-561">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8313b-561">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8313b-562">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-562">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-563">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-563">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-564">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="8313b-564">`OperationService` operations:</span></span>

<span data-ttu-id="8313b-565">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="8313b-565">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="8313b-566">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="8313b-566">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="8313b-567">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-567">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-568">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-568">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-569">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="8313b-569">**Second request:**</span></span>

<span data-ttu-id="8313b-570">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="8313b-570">Controller operations:</span></span>

<span data-ttu-id="8313b-571">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="8313b-571">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="8313b-572">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8313b-572">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8313b-573">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-573">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-574">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-574">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-575">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="8313b-575">`OperationService` operations:</span></span>

<span data-ttu-id="8313b-576">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="8313b-576">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="8313b-577">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="8313b-577">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="8313b-578">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="8313b-578">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="8313b-579">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="8313b-579">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="8313b-580">Zauważ, że wartości `OperationId` różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="8313b-580">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="8313b-581">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="8313b-581">*Transient* objects are always different.</span></span> <span data-ttu-id="8313b-582">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="8313b-582">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="8313b-583">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="8313b-583">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="8313b-584">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="8313b-584">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="8313b-585">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, `Operation` czy wystąpienie jest dostarczone `Startup.ConfigureServices`w.</span><span class="sxs-lookup"><span data-stu-id="8313b-585">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="8313b-586">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="8313b-586">Call services from main</span></span>

<span data-ttu-id="8313b-587">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-587">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="8313b-588">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="8313b-588">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="8313b-589">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="8313b-589">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="8313b-590">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="8313b-590">Scope validation</span></span>

<span data-ttu-id="8313b-591">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="8313b-591">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8313b-592">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="8313b-592">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="8313b-593">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="8313b-593">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="8313b-594">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="8313b-594">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="8313b-595">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-595">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8313b-596">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="8313b-596">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8313b-597">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="8313b-597">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="8313b-598">Sprawdzanie poprawności zakresów usług przechwytuje te `BuildServiceProvider` sytuacje, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="8313b-598">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8313b-599">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="8313b-599">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="8313b-600">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="8313b-600">Request Services</span></span>

<span data-ttu-id="8313b-601">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="8313b-601">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="8313b-602">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-602">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="8313b-603">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices`, nie. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="8313b-603">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="8313b-604">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="8313b-604">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="8313b-605">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="8313b-605">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="8313b-606">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="8313b-606">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="8313b-607">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp `RequestServices` do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8313b-607">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="8313b-608">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="8313b-608">Design services for dependency injection</span></span>

<span data-ttu-id="8313b-609">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="8313b-609">Best practices are to:</span></span>

* <span data-ttu-id="8313b-610">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="8313b-610">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="8313b-611">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="8313b-611">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="8313b-612">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="8313b-612">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="8313b-613">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-613">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="8313b-614">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-614">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="8313b-615">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="8313b-615">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="8313b-616">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="8313b-616">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="8313b-617">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="8313b-617">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="8313b-618">Należy pamiętać, że Razor klasy klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8313b-618">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="8313b-619">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="8313b-619">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="8313b-620">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="8313b-620">Disposal of services</span></span>

<span data-ttu-id="8313b-621">Kontener wywołuje <xref:System.IDisposable.Dispose*> typy, które <xref:System.IDisposable> tworzy.</span><span class="sxs-lookup"><span data-stu-id="8313b-621">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="8313b-622">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="8313b-622">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="8313b-623">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="8313b-623">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="8313b-624">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8313b-624">In the following example:</span></span>

* <span data-ttu-id="8313b-625">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-625">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="8313b-626">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="8313b-626">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="8313b-627">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="8313b-627">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="8313b-628">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8313b-628">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="8313b-629">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="8313b-629">Default service container replacement</span></span>

<span data-ttu-id="8313b-630">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="8313b-630">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="8313b-631">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="8313b-631">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="8313b-632">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="8313b-632">Property injection</span></span>
* <span data-ttu-id="8313b-633">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="8313b-633">Injection based on name</span></span>
* <span data-ttu-id="8313b-634">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="8313b-634">Child containers</span></span>
* <span data-ttu-id="8313b-635">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="8313b-635">Custom lifetime management</span></span>
* <span data-ttu-id="8313b-636">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="8313b-636">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="8313b-637">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="8313b-637">Convention-based registration</span></span>

<span data-ttu-id="8313b-638">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="8313b-638">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="8313b-639">Autofac</span><span class="sxs-lookup"><span data-stu-id="8313b-639">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="8313b-640">DryIoc</span><span class="sxs-lookup"><span data-stu-id="8313b-640">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="8313b-641">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="8313b-641">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="8313b-642">LightInject</span><span class="sxs-lookup"><span data-stu-id="8313b-642">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="8313b-643">Lamar</span><span class="sxs-lookup"><span data-stu-id="8313b-643">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="8313b-644">Stashbox</span><span class="sxs-lookup"><span data-stu-id="8313b-644">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="8313b-645">Unity</span><span class="sxs-lookup"><span data-stu-id="8313b-645">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="8313b-646">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="8313b-646">Thread safety</span></span>

<span data-ttu-id="8313b-647">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="8313b-647">Create thread-safe singleton services.</span></span> <span data-ttu-id="8313b-648">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="8313b-648">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="8313b-649">Metoda fabryki pojedynczej usługi, taka jak drugi argument dla [\<addsingleton TService> (IServiceCollection, Func\<IServiceProvider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="8313b-649">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="8313b-650">Podobnie jak w przypadku`static`konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="8313b-650">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8313b-651">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="8313b-651">Recommendations</span></span>

* <span data-ttu-id="8313b-652">`async/await`Rozpoznawanie `Task` usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8313b-652">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="8313b-653">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-653">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="8313b-654">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-654">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="8313b-655">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="8313b-655">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="8313b-656">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8313b-656">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8313b-657">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="8313b-657">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="8313b-658">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="8313b-658">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="8313b-659">Należy unikać statycznego dostępu do usług (na przykład statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="8313b-659">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="8313b-660">Unikaj używania *wzorca lokalizatora usługi*, który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="8313b-660">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="8313b-661">Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="8313b-661">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="8313b-662">**Prawidłowy**</span><span class="sxs-lookup"><span data-stu-id="8313b-662">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="8313b-663">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="8313b-663">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="8313b-664">Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie <xref:System.IServiceProvider.GetService*>wykonywania za pomocą polecenia.</span><span class="sxs-lookup"><span data-stu-id="8313b-664">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="8313b-665">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="8313b-665">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="8313b-666">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="8313b-666">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="8313b-667">Wyjątki są rzadko&mdash;w większości specjalnych przypadków w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="8313b-667">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="8313b-668">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="8313b-668">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="8313b-669">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="8313b-669">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8313b-670">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8313b-670">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="8313b-671">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="8313b-671">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="8313b-672">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="8313b-672">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="8313b-673">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="8313b-673">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="8313b-674">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="8313b-674">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
