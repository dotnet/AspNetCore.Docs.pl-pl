---
title: Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core implementuje iniekcję zależności i jak go używać.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 4e990329b7ebcfc9cbbff8a3c9895604a22461d3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661697"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="05561-103">Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="05561-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="05561-104">Przez [Steve Smith](https://ardalis.com/) i Scott [Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="05561-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="05561-105">ASP.NET Core obsługuje wzorzec projektowania oprogramowania wtrysku zależności (DI), który jest techniką osiągnięcia [inwersji kontroli (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="05561-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="05561-106">Aby uzyskać więcej informacji dotyczących iniekcji <xref:mvc/controllers/dependency-injection>zależności w kontrolerach MVC, zobacz .</span><span class="sxs-lookup"><span data-stu-id="05561-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="05561-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05561-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="05561-108">Omówienie iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="05561-108">Overview of dependency injection</span></span>

<span data-ttu-id="05561-109">*Zależność* jest dowolny obiekt, który wymaga innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="05561-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="05561-110">Sprawdź następującą `MyDependency` klasę `WriteMessage` za pomocą metody, od których zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="05561-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="05561-111">Wystąpienie klasy `MyDependency` można utworzyć, aby `WriteMessage` udostępnić metodę do klasy.</span><span class="sxs-lookup"><span data-stu-id="05561-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="05561-112">Klasa `MyDependency` jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="05561-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="05561-113">Klasa tworzy i bezpośrednio zależy `MyDependency` od wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="05561-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="05561-114">Zależności kodu (takie jak poprzedni przykład) są problematyczne i należy ich unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="05561-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="05561-115">Aby `MyDependency` zastąpić inną implementację, klasa musi zostać zmodyfikowana.</span><span class="sxs-lookup"><span data-stu-id="05561-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="05561-116">Jeśli `MyDependency` ma zależności, muszą być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="05561-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="05561-117">W dużym projekcie z wieloma `MyDependency`klasami w zależności od kodu konfiguracji staje się rozproszone w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="05561-118">Ta implementacja jest trudne do jednostkowego testu.</span><span class="sxs-lookup"><span data-stu-id="05561-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="05561-119">Aplikacja powinna używać klasy `MyDependency` makiety lub skrótowej, co nie jest możliwe w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="05561-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="05561-120">Iniekcja zależności rozwiązuje następujące problemy poprzez:</span><span class="sxs-lookup"><span data-stu-id="05561-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="05561-121">Użycie interfejsu lub klasy podstawowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="05561-122">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="05561-123">ASP.NET Core zapewnia wbudowany kontener serwisowy, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="05561-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="05561-124">Usługi są rejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="05561-125">*Wtrysk* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="05561-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="05561-126">Struktura bierze na siebie odpowiedzialność tworzenia wystąpienia zależności i usuwania go, gdy nie jest już potrzebne.</span><span class="sxs-lookup"><span data-stu-id="05561-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="05561-127">W [przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)aplikacji `IMyDependency` interfejs definiuje metodę, którą usługa zapewnia aplikacji:</span><span class="sxs-lookup"><span data-stu-id="05561-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="05561-128">Ten interfejs jest realizowany przez `MyDependency`konkretny typ:</span><span class="sxs-lookup"><span data-stu-id="05561-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="05561-129">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w jego konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="05561-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="05561-130">Nie jest niczym niezwykłym, aby użyć iniekcji zależności w sposób łańcuchowy.</span><span class="sxs-lookup"><span data-stu-id="05561-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="05561-131">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="05561-132">Kontener rozpoznaje zależności na wykresie i zwraca w pełni rozwiązaną usługę.</span><span class="sxs-lookup"><span data-stu-id="05561-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="05561-133">Zbiorczy zestaw zależności, które muszą zostać rozwiązane, jest zazwyczaj określany jako *drzewo zależności,* *wykres zależności*lub *wykres obiektu.*</span><span class="sxs-lookup"><span data-stu-id="05561-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="05561-134">`IMyDependency`i `ILogger<TCategoryName>` muszą być zarejestrowane w kontenerze serwisowym.</span><span class="sxs-lookup"><span data-stu-id="05561-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="05561-135">`IMyDependency`jest zarejestrowana w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="05561-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05561-136">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [usługa świadczona przez framework](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="05561-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="05561-137">Kontener jest `ILogger<TCategoryName>` rozwiązywany, korzystając z [(ogólnych) otwartych typów,](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)eliminując konieczność rejestrowania każdego [(ogólnego) typu skonstruowanego:](/dotnet/csharp/language-reference/language-specification/types#constructed-types)</span><span class="sxs-lookup"><span data-stu-id="05561-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="05561-138">W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana `MyDependency`z typem betonu .</span><span class="sxs-lookup"><span data-stu-id="05561-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="05561-139">Zakresy rejestracji okres istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="05561-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="05561-140">[Okresy istnienia usługi](#service-lifetimes) są opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="05561-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="05561-141">Każda `services.Add{SERVICE_NAME}` metoda rozszerzenia dodaje (i potencjalnie konfiguruje) usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="05561-142">Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC wymagają.</span><span class="sxs-lookup"><span data-stu-id="05561-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="05561-143">Zaleca się, aby aplikacje postępowali zgodnie z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="05561-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="05561-144">Umieść metody rozszerzenia w obszarze nazw [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="05561-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="05561-145">Jeśli konstruktor usługi wymaga [wbudowanego typu,](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`takiego jak , typ może zostać wstrzyknięty za pomocą [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji:](xref:fundamentals/configuration/options)</span><span class="sxs-lookup"><span data-stu-id="05561-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="05561-146">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której usługa jest używana i przypisana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="05561-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="05561-147">Pole służy do uzyskiwania dostępu do usługi w razie potrzeby w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="05561-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="05561-148">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane `WriteMessage` do wywoływania metody usługi:</span><span class="sxs-lookup"><span data-stu-id="05561-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="05561-149">Usługi wstrzyknięte do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="05561-149">Services injected into Startup</span></span>

<span data-ttu-id="05561-150">Tylko następujące typy usług mogą `Startup` być wstrzykiwane do konstruktora podczas korzystania z hosta ogólnego (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="05561-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="05561-151">Usługi można wstrzyknąć do: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="05561-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="05561-152">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="05561-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="05561-153">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="05561-153">Framework-provided services</span></span>

<span data-ttu-id="05561-154">Metoda `Startup.ConfigureServices` jest odpowiedzialna za definiowanie usług, które aplikacja używa, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="05561-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="05561-155">Początkowo `IServiceCollection` dostarczone ma `ConfigureServices` usługi zdefiniowane przez strukturę w zależności od sposobu [hosta został skonfigurowany](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="05561-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="05561-156">Nie jest rzadkością dla aplikacji na podstawie szablonu core ASP.NET mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="05561-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="05561-157">W poniższej tabeli wymieniono niewielką próbkę usług zarejestrowanych w ramach.</span><span class="sxs-lookup"><span data-stu-id="05561-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="05561-158">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="05561-158">Service Type</span></span> | <span data-ttu-id="05561-159">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="05561-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="05561-160">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="05561-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="05561-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="05561-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="05561-164">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="05561-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="05561-166">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="05561-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="05561-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="05561-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="05561-170">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="05561-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="05561-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="05561-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="05561-174">Rejestrowanie dodatkowych usług przy pomocy metod rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="05561-174">Register additional services with extension methods</span></span>

<span data-ttu-id="05561-175">Gdy metoda rozszerzenia kolekcji usług jest dostępna do zarejestrowania usługi (i jej usług `Add{SERVICE_NAME}` zależnych, jeśli jest to wymagane), konwencja jest użycie metody pojedynczego rozszerzenia, aby zarejestrować wszystkie usługi wymagane przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="05561-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="05561-176">Poniższy kod jest przykładem dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzenia [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="05561-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="05561-177">Aby uzyskać więcej <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> informacji, zobacz klasę w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05561-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="05561-178">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="05561-178">Service lifetimes</span></span>

<span data-ttu-id="05561-179">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="05561-180">ASP.NET usługi Core można skonfigurować z następującymi okresami istnienia:</span><span class="sxs-lookup"><span data-stu-id="05561-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="05561-181">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-181">Transient</span></span>

<span data-ttu-id="05561-182">Usługi przejściowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>lifetime ( ) są tworzone za każdym razem, gdy są wymagane z kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="05561-183">Ten okres istnienia działa najlepiej w przypadku lekkich, bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="05561-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="05561-184">Zakresu</span><span class="sxs-lookup"><span data-stu-id="05561-184">Scoped</span></span>

<span data-ttu-id="05561-185">Usługi okresowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>o określonym zakresie ( ) są tworzone raz na żądanie klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="05561-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="05561-186">Podczas korzystania z usługi o określonym zakresie w `Invoke` oprogramowaniu pośredniczącym, wstrzyknąć usługę do lub `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="05561-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="05561-187">Nie wstrzyknąć za pomocą [iniekcji konstruktora,](xref:mvc/controllers/dependency-injection#constructor-injection) ponieważ wymusza usługę zachowywać się jak singleton.</span><span class="sxs-lookup"><span data-stu-id="05561-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="05561-188">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="05561-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="05561-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-189">Singleton</span></span>

<span data-ttu-id="05561-190">Usługi okresu istnienia<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) są tworzone po `Startup.ConfigureServices` raz pierwszy są wymagane (lub po uruchomieniu i wystąpienie jest określony z rejestracją usługi).</span><span class="sxs-lookup"><span data-stu-id="05561-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="05561-191">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="05561-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="05561-192">Jeśli aplikacja wymaga zachowania singleton, zaleca się zezwalanie kontenerowi usługi na zarządzanie okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="05561-193">Nie implementuj wzorzec projektu singleton i podaj kod użytkownika do zarządzania okres istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="05561-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="05561-194">Jest to niebezpieczne, aby rozwiązać usługi o określonym zakresie z singleton.</span><span class="sxs-lookup"><span data-stu-id="05561-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="05561-195">Może to spowodować, że usługa ma niepoprawny stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="05561-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="05561-196">Metody rejestracji usługi</span><span class="sxs-lookup"><span data-stu-id="05561-196">Service registration methods</span></span>

<span data-ttu-id="05561-197">Metody rozszerzenia rejestracji usługi oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="05561-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="05561-198">Metoda</span><span class="sxs-lookup"><span data-stu-id="05561-198">Method</span></span> | <span data-ttu-id="05561-199">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="05561-199">Automatic</span></span><br><span data-ttu-id="05561-200">obiekt</span><span class="sxs-lookup"><span data-stu-id="05561-200">object</span></span><br><span data-ttu-id="05561-201">Dyspozycji</span><span class="sxs-lookup"><span data-stu-id="05561-201">disposal</span></span> | <span data-ttu-id="05561-202">Wiele</span><span class="sxs-lookup"><span data-stu-id="05561-202">Multiple</span></span><br><span data-ttu-id="05561-203">implementacje</span><span class="sxs-lookup"><span data-stu-id="05561-203">implementations</span></span> | <span data-ttu-id="05561-204">Przekaż args</span><span class="sxs-lookup"><span data-stu-id="05561-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="05561-205">Przykład:</span><span class="sxs-lookup"><span data-stu-id="05561-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="05561-206">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-206">Yes</span></span> | <span data-ttu-id="05561-207">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-207">Yes</span></span> | <span data-ttu-id="05561-208">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="05561-209">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="05561-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="05561-210">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-210">Yes</span></span> | <span data-ttu-id="05561-211">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-211">Yes</span></span> | <span data-ttu-id="05561-212">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="05561-213">Przykład:</span><span class="sxs-lookup"><span data-stu-id="05561-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="05561-214">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-214">Yes</span></span> | <span data-ttu-id="05561-215">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-215">No</span></span> | <span data-ttu-id="05561-216">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="05561-217">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="05561-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="05561-218">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-218">No</span></span> | <span data-ttu-id="05561-219">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-219">Yes</span></span> | <span data-ttu-id="05561-220">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="05561-221">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="05561-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="05561-222">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-222">No</span></span> | <span data-ttu-id="05561-223">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-223">No</span></span> | <span data-ttu-id="05561-224">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-224">Yes</span></span> |

<span data-ttu-id="05561-225">Aby uzyskać więcej informacji na temat usuwania typu, zobacz [usuwanie usług](#disposal-of-services) sekcji.</span><span class="sxs-lookup"><span data-stu-id="05561-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="05561-226">Typowym scenariuszem dla wielu implementacji jest [szydercze typy do testowania.](xref:test/integration-tests#inject-mock-services)</span><span class="sxs-lookup"><span data-stu-id="05561-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="05561-227">`TryAdd{LIFETIME}`metody zarejestrować usługę tylko wtedy, gdy nie ma jeszcze zarejestrowanej implementacji.</span><span class="sxs-lookup"><span data-stu-id="05561-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="05561-228">W poniższym przykładzie pierwszy `MyDependency` wiersz `IMyDependency`rejestruje się dla .</span><span class="sxs-lookup"><span data-stu-id="05561-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="05561-229">Drugi wiersz nie ma `IMyDependency` wpływu, ponieważ ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="05561-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="05561-230">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="05561-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="05561-231">[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie ma jeszcze implementacji *tego samego typu.*</span><span class="sxs-lookup"><span data-stu-id="05561-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="05561-232">Wiele usług jest `IEnumerable<{SERVICE}>`rozwiązywanych za pośrednictwem pliku .</span><span class="sxs-lookup"><span data-stu-id="05561-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="05561-233">Podczas rejestrowania usług deweloper chce dodać wystąpienie tylko wtedy, gdy jeden z tego samego typu nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="05561-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="05561-234">Ogólnie rzecz biorąc ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="05561-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="05561-235">W poniższym przykładzie pierwszy `MyDep` wiersz `IMyDep1`rejestruje się dla .</span><span class="sxs-lookup"><span data-stu-id="05561-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="05561-236">Druga linia rejestruje `MyDep` `IMyDep2`się dla .</span><span class="sxs-lookup"><span data-stu-id="05561-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="05561-237">Trzecia linia nie ma `IMyDep1` wpływu, ponieważ `MyDep`ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="05561-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="05561-238">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="05561-238">Constructor injection behavior</span></span>

<span data-ttu-id="05561-239">Usługi można rozwiązać za pomocą dwóch mechanizmów:</span><span class="sxs-lookup"><span data-stu-id="05561-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="05561-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="05561-241">`ActivatorUtilities`jest używany z abstrakcjami skierowanymi do użytkownika, takimi jak Pomocników tagów, kontrolery MVC i spinacze modelu.</span><span class="sxs-lookup"><span data-stu-id="05561-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="05561-242">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="05561-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="05561-243">Gdy usługi są `IServiceProvider` rozpoznawane przez lub `ActivatorUtilities` [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego.*</span><span class="sxs-lookup"><span data-stu-id="05561-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="05561-244">Gdy usługi są `ActivatorUtilities`rozpoznawane przez [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, że istnieje tylko jeden odpowiedni konstruktor.</span><span class="sxs-lookup"><span data-stu-id="05561-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="05561-245">Przeciążenia konstruktora są obsługiwane, ale tylko jedno przeciążenie może istnieć, których argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="05561-246">Konteksty struktury encji</span><span class="sxs-lookup"><span data-stu-id="05561-246">Entity Framework contexts</span></span>

<span data-ttu-id="05561-247">Konteksty entity framework są zwykle dodawane do kontenera usługi przy użyciu [okresu istnienia o określonym zakresie,](#service-lifetimes) ponieważ operacje bazy danych aplikacji sieci web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="05561-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="05561-248">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) przeciążenie podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="05561-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="05561-249">Usługi danego okresu istnienia nie należy używać kontekstu bazy danych o krótszym okresie istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="05561-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="05561-250">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="05561-250">Lifetime and registration options</span></span>

<span data-ttu-id="05561-251">Aby zademonstrować różnicę między opcjami okresu istnienia i rejestracji, należy wziąć pod uwagę `OperationId`następujące interfejsy, które reprezentują zadania jako operację z unikatowym identyfikatorem .</span><span class="sxs-lookup"><span data-stu-id="05561-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="05561-252">W zależności od tego, jak okres istnienia usługi operacyjnej jest skonfigurowany dla następujących interfejsów, kontener udostępnia takie samo lub inne wystąpienie usługi, gdy jest to wymagane przez klasę:</span><span class="sxs-lookup"><span data-stu-id="05561-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="05561-253">Interfejsy są implementowane `Operation` w klasie.</span><span class="sxs-lookup"><span data-stu-id="05561-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="05561-254">Konstruktor `Operation` generuje identyfikator GUID, jeśli nie jest dostarczany:</span><span class="sxs-lookup"><span data-stu-id="05561-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="05561-255">An `OperationService` jest zarejestrowany, który zależy `Operation` od każdego z innych typów.</span><span class="sxs-lookup"><span data-stu-id="05561-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="05561-256">Gdy `OperationService` jest wymagane za pośrednictwem iniekcji zależności, odbiera nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="05561-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="05561-257">Gdy usługi przejściowe są tworzone na żądanie `OperationId` z `IOperationTransient` kontenera, `OperationId` usługa `OperationService`jest inna niż z .</span><span class="sxs-lookup"><span data-stu-id="05561-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="05561-258">`OperationService`otrzymuje nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="05561-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="05561-259">Nowe wystąpienie daje `OperationId`inny .</span><span class="sxs-lookup"><span data-stu-id="05561-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="05561-260">Gdy usługi o określonym zakresie są `OperationId` tworzone `IOperationScoped` na żądanie klienta, `OperationService` usługa jest taka sama jak w żądaniu klienta.</span><span class="sxs-lookup"><span data-stu-id="05561-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="05561-261">W przypadku żądań klientów `OperationId` obie usługi mają inną wartość.</span><span class="sxs-lookup"><span data-stu-id="05561-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="05561-262">Gdy usługi pojedynczego i pojedynczego wystąpienia są tworzone raz i używane `OperationId` we wszystkich żądaniach klientów i wszystkich usługach, jest stała we wszystkich żądaniach usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="05561-263">W `Startup.ConfigureServices`, każdy typ jest dodawany do kontenera zgodnie z jego nazwany okres istnienia:</span><span class="sxs-lookup"><span data-stu-id="05561-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="05561-264">Usługa `IOperationSingletonInstance` używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="05561-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="05561-265">Jest jasne, gdy ten typ jest w użyciu (jego identyfikator GUID jest wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="05561-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="05561-266">Przykładowa aplikacja pokazuje okres istnienia obiektu w obrębie i między poszczególnymi żądaniami.</span><span class="sxs-lookup"><span data-stu-id="05561-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="05561-267">Przykładowa aplikacja `IndexModel` żąda każdego `IOperation` rodzaju typu `OperationService`i .</span><span class="sxs-lookup"><span data-stu-id="05561-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="05561-268">Następnie strona wyświetla wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="05561-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="05561-269">Dwa następujące dane wyjściowe pokazuje wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="05561-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="05561-270">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="05561-270">**First request:**</span></span>

<span data-ttu-id="05561-271">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="05561-271">Controller operations:</span></span>

<span data-ttu-id="05561-272">Przemijające: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="05561-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="05561-273">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="05561-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="05561-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-275">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-276">`OperationService`Operacji:</span><span class="sxs-lookup"><span data-stu-id="05561-276">`OperationService` operations:</span></span>

<span data-ttu-id="05561-277">Przemijające: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="05561-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="05561-278">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="05561-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="05561-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-280">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-281">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="05561-281">**Second request:**</span></span>

<span data-ttu-id="05561-282">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="05561-282">Controller operations:</span></span>

<span data-ttu-id="05561-283">Przemijające: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="05561-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="05561-284">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="05561-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="05561-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-286">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-287">`OperationService`Operacji:</span><span class="sxs-lookup"><span data-stu-id="05561-287">`OperationService` operations:</span></span>

<span data-ttu-id="05561-288">Przejściowe: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="05561-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="05561-289">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="05561-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="05561-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-291">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-292">Należy przestrzegać, `OperationId` które z wartości różnią się w obrębie żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="05561-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="05561-293">*Obiekty przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="05561-293">*Transient* objects are always different.</span></span> <span data-ttu-id="05561-294">Wartość przejściowa `OperationId` dla żądań pierwszego i drugiego `OperationService` klienta są różne dla operacji i żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="05561-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="05561-295">Nowe wystąpienie jest dostarczane do każdego żądania usługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="05561-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="05561-296">*Obiekty o określonym zakresie* są takie same w żądaniu klienta, ale różne w żądaniach klientów.</span><span class="sxs-lookup"><span data-stu-id="05561-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="05561-297">*Singleton* obiekty są takie same dla każdego obiektu `Operation` i każdego `Startup.ConfigureServices`żądania, niezależnie od tego, czy wystąpienie jest przewidziane w .</span><span class="sxs-lookup"><span data-stu-id="05561-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="05561-298">Zadzwoń do usług z głównych</span><span class="sxs-lookup"><span data-stu-id="05561-298">Call services from main</span></span>

<span data-ttu-id="05561-299">Utwórz <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> za pomocą [IServiceScopeFactory.CreateScope,](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) aby rozwiązać usługi o określonym zakresie w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="05561-300">Takie podejście jest przydatne do uzyskania dostępu do usługi o określonym zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="05561-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="05561-301">W poniższym przykładzie pokazano, `MyScopedService` jak `Program.Main`uzyskać kontekst dla w:</span><span class="sxs-lookup"><span data-stu-id="05561-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="05561-302">Sprawdzanie poprawności zakresu</span><span class="sxs-lookup"><span data-stu-id="05561-302">Scope validation</span></span>

<span data-ttu-id="05561-303">Gdy aplikacja jest uruchomiona w środowisku deweloperskim i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do utworzenia hosta, domyślny dostawca usług wykonuje kontrole, aby sprawdzić, czy:</span><span class="sxs-lookup"><span data-stu-id="05561-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="05561-304">Usługi o określonym zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez głównego dostawcę usług.</span><span class="sxs-lookup"><span data-stu-id="05561-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="05561-305">Usługi o określonym zakresie nie są bezpośrednio lub pośrednio wstrzykuje się do singletons.</span><span class="sxs-lookup"><span data-stu-id="05561-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="05561-306">Główny dostawca usług jest <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> tworzony, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="05561-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="05561-307">Okres istnienia głównego dostawcy usług odpowiada okresowi istnienia aplikacji/serwera, gdy dostawca uruchamia się z aplikacją i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="05561-308">Usługi o określonym zakresie są usuwane przez kontener, który je utworzył.</span><span class="sxs-lookup"><span data-stu-id="05561-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="05561-309">Jeśli usługa o określonym zakresie jest tworzona w kontenerze głównym, okres istnienia usługi jest skutecznie promowany do singleton, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest zamknięty.</span><span class="sxs-lookup"><span data-stu-id="05561-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="05561-310">Sprawdzanie poprawności zakresów usługi łapie te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="05561-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="05561-311">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="05561-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="05561-312">Żądania usług</span><span class="sxs-lookup"><span data-stu-id="05561-312">Request Services</span></span>

<span data-ttu-id="05561-313">Usługi dostępne w ramach żądania core `HttpContext` ASP.NET są udostępniane za pośrednictwem [httpContext.RequestServices kolekcji.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)</span><span class="sxs-lookup"><span data-stu-id="05561-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="05561-314">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="05561-315">Gdy obiekty określają zależności, są one spełnione `RequestServices`przez `ApplicationServices`typy znalezione w , nie .</span><span class="sxs-lookup"><span data-stu-id="05561-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="05561-316">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="05561-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="05561-317">Zamiast tego zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas i zezwalaj na platformę, aby wstrzyknąć zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="05561-318">Daje to klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="05561-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="05561-319">Preferuj żądanie zależności jako parametry konstruktora dostępu do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="05561-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="05561-320">Usługi projektowe do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="05561-320">Design services for dependency injection</span></span>

<span data-ttu-id="05561-321">Najważniejsze wskazówki to:</span><span class="sxs-lookup"><span data-stu-id="05561-321">Best practices are to:</span></span>

* <span data-ttu-id="05561-322">Projektowanie usług do korzystania z iniekcji zależności, aby uzyskać ich zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="05561-323">Unikaj klas stanowych, statycznych i członków.</span><span class="sxs-lookup"><span data-stu-id="05561-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="05561-324">Projektowanie aplikacji do korzystania z usług singleton zamiast tego, które unikają tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="05561-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="05561-325">Unikaj bezpośredniego tworzenia wystąpienia klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="05561-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="05561-326">Bezpośrednie tworzenie wystąpienia łączy kod z określoną implementacją.</span><span class="sxs-lookup"><span data-stu-id="05561-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="05561-327">Spraw, aby klasy aplikacji były małe, dobrze uwzględnione i łatwe do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="05561-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="05561-328">Jeśli klasa wydaje się mieć zbyt wiele wstrzykniętych zależności, jest to zazwyczaj znak, że klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="05561-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="05561-329">Spróbuj refaktoryzowania klasy, przenosząc niektóre z jego obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="05561-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="05561-330">Należy pamiętać, że klasy modelu strony Razor Pages i klasy kontrolera MVC powinny koncentrować się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="05561-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="05561-331">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [odrębnych problemów.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)</span><span class="sxs-lookup"><span data-stu-id="05561-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="05561-332">Unieszkodliwianie usług</span><span class="sxs-lookup"><span data-stu-id="05561-332">Disposal of services</span></span>

<span data-ttu-id="05561-333">Kontener wymaga <xref:System.IDisposable.Dispose*> typów, <xref:System.IDisposable> które tworzy.</span><span class="sxs-lookup"><span data-stu-id="05561-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="05561-334">Jeśli wystąpienie zostanie dodane do kontenera przez kod użytkownika, nie jest usuwany automatycznie.</span><span class="sxs-lookup"><span data-stu-id="05561-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="05561-335">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="05561-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="05561-336">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="05561-336">In the following example:</span></span>

* <span data-ttu-id="05561-337">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="05561-338">Okres istnienia zamierzonej usługi nie są znane przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="05561-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="05561-339">Struktura nie usuwa usług automatycznie.</span><span class="sxs-lookup"><span data-stu-id="05561-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="05561-340">Jeśli usługi nie są jawnie usuwane w kodzie dewelopera, utrzymują się, dopóki aplikacja nie zostanie zamknięta.</span><span class="sxs-lookup"><span data-stu-id="05561-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="05561-341">Aby zapoznać się z omówieniem opcji usuwania usług, zobacz [Cztery sposoby usuwania identyfikatorów w ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="05561-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="05561-342">Domyślna wymiana kontenera usługi</span><span class="sxs-lookup"><span data-stu-id="05561-342">Default service container replacement</span></span>

<span data-ttu-id="05561-343">Wbudowany kontener usługi został zaprojektowany do obsługi potrzeb struktury i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="05561-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="05561-344">Zaleca się użycie wbudowanego kontenera, chyba że potrzebna jest określona funkcja, której wbudowany kontener nie obsługuje, na przykład:</span><span class="sxs-lookup"><span data-stu-id="05561-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="05561-345">Wstrzyknięcie właściwości</span><span class="sxs-lookup"><span data-stu-id="05561-345">Property injection</span></span>
* <span data-ttu-id="05561-346">Wstrzyknięcie na podstawie nazwy</span><span class="sxs-lookup"><span data-stu-id="05561-346">Injection based on name</span></span>
* <span data-ttu-id="05561-347">Pojemniki dla dzieci</span><span class="sxs-lookup"><span data-stu-id="05561-347">Child containers</span></span>
* <span data-ttu-id="05561-348">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="05561-348">Custom lifetime management</span></span>
* <span data-ttu-id="05561-349">`Func<T>`obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="05561-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="05561-350">Rejestracja oparta na konwencji</span><span class="sxs-lookup"><span data-stu-id="05561-350">Convention-based registration</span></span>

<span data-ttu-id="05561-351">Z ASP.NET aplikacjami Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="05561-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="05561-352">Autofac (autofac)</span><span class="sxs-lookup"><span data-stu-id="05561-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="05561-353">Suchość</span><span class="sxs-lookup"><span data-stu-id="05561-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="05561-354">Grace</span><span class="sxs-lookup"><span data-stu-id="05561-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="05561-355">Lekka Inject</span><span class="sxs-lookup"><span data-stu-id="05561-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="05561-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="05561-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="05561-357">Skrytka</span><span class="sxs-lookup"><span data-stu-id="05561-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="05561-358">Unity</span><span class="sxs-lookup"><span data-stu-id="05561-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="05561-359">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="05561-359">Thread safety</span></span>

<span data-ttu-id="05561-360">Tworzenie usług singleton bezpiecznych dla wątków.</span><span class="sxs-lookup"><span data-stu-id="05561-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="05561-361">Jeśli usługa singleton ma zależność od usługi przejściowej, usługi przejściowe mogą również wymagać bezpieczeństwa wątku w zależności od tego, jak jest używany przez singleton.</span><span class="sxs-lookup"><span data-stu-id="05561-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="05561-362">Metoda fabryczna pojedynczej usługi, takich jak drugi argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczne dla wątków.</span><span class="sxs-lookup"><span data-stu-id="05561-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="05561-363">Podobnie jak`static`konstruktor typu ( ), jest gwarantowane, aby być wywoływane raz przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="05561-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="05561-364">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="05561-364">Recommendations</span></span>

* <span data-ttu-id="05561-365">`async/await`i `Task` rozdzielczość usługi nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="05561-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="05561-366">C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec jest użycie metod asynchronicznych po synchronicznie rozwiązywania usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="05561-367">Należy unikać przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="05561-368">Na przykład koszyk użytkownika zazwyczaj nie powinny być dodawane do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="05561-369">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="05561-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="05561-370">Podobnie należy unikać "posiadacza danych" obiektów, które istnieją tylko w celu umożliwienia dostępu do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="05561-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="05561-371">Lepiej jest poprosić o rzeczywisty element za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="05561-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="05561-372">Unikaj statycznego dostępu do usług (na przykład statycznie wpisując [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użycia w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="05561-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="05561-373">Należy unikać używania *wzorca lokalizatora usług*.</span><span class="sxs-lookup"><span data-stu-id="05561-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="05561-374">Na przykład nie wywoływać, aby uzyskać wystąpienie <xref:System.IServiceProvider.GetService*> usługi, gdy można użyć DI zamiast:</span><span class="sxs-lookup"><span data-stu-id="05561-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="05561-375">**Niepoprawne:**</span><span class="sxs-lookup"><span data-stu-id="05561-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="05561-376">**Prawidłowe:**</span><span class="sxs-lookup"><span data-stu-id="05561-376">**Correct**:</span></span>

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

* <span data-ttu-id="05561-377">Inną odmianą lokalizatora usług, których należy unikać, jest wstrzykiwanie fabryki, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="05561-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="05561-378">Obie te praktyki [mieszają inwersję](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategii kontroli.</span><span class="sxs-lookup"><span data-stu-id="05561-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="05561-379">Unikaj dostępu statycznego (na `HttpContext` przykład [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="05561-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="05561-380">Podobnie jak wszystkie zestawy zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="05561-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="05561-381">Wyjątki są&mdash;rzadkie głównie szczególnych przypadkach w samych ramach.</span><span class="sxs-lookup"><span data-stu-id="05561-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="05561-382">DI jest *alternatywą dla* wzorców dostępu do obiektów statycznych/globalnych.</span><span class="sxs-lookup"><span data-stu-id="05561-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="05561-383">Możesz nie być w stanie zrealizować korzyści z DI, jeśli mieszasz go z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="05561-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05561-384">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="05561-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="05561-385">Pisanie czystego kodu w ASP.NET Core z iniekcji zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="05561-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="05561-386">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="05561-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="05561-387">Odwrócenie kontenerów kontrolnych i wzorzec iniekcji zależności (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="05561-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="05561-388">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="05561-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="05561-389">ASP.NET Core obsługuje wzorzec projektowania oprogramowania wtrysku zależności (DI), który jest techniką osiągnięcia [inwersji kontroli (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="05561-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="05561-390">Aby uzyskać więcej informacji dotyczących iniekcji <xref:mvc/controllers/dependency-injection>zależności w kontrolerach MVC, zobacz .</span><span class="sxs-lookup"><span data-stu-id="05561-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="05561-391">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05561-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="05561-392">Omówienie iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="05561-392">Overview of dependency injection</span></span>

<span data-ttu-id="05561-393">*Zależność* jest dowolny obiekt, który wymaga innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="05561-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="05561-394">Sprawdź następującą `MyDependency` klasę `WriteMessage` za pomocą metody, od których zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="05561-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="05561-395">Wystąpienie klasy `MyDependency` można utworzyć, aby `WriteMessage` udostępnić metodę do klasy.</span><span class="sxs-lookup"><span data-stu-id="05561-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="05561-396">Klasa `MyDependency` jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="05561-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="05561-397">Klasa tworzy i bezpośrednio zależy `MyDependency` od wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="05561-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="05561-398">Zależności kodu (takie jak poprzedni przykład) są problematyczne i należy ich unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="05561-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="05561-399">Aby `MyDependency` zastąpić inną implementację, klasa musi zostać zmodyfikowana.</span><span class="sxs-lookup"><span data-stu-id="05561-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="05561-400">Jeśli `MyDependency` ma zależności, muszą być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="05561-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="05561-401">W dużym projekcie z wieloma `MyDependency`klasami w zależności od kodu konfiguracji staje się rozproszone w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="05561-402">Ta implementacja jest trudne do jednostkowego testu.</span><span class="sxs-lookup"><span data-stu-id="05561-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="05561-403">Aplikacja powinna używać klasy `MyDependency` makiety lub skrótowej, co nie jest możliwe w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="05561-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="05561-404">Iniekcja zależności rozwiązuje następujące problemy poprzez:</span><span class="sxs-lookup"><span data-stu-id="05561-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="05561-405">Użycie interfejsu lub klasy podstawowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="05561-406">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="05561-407">ASP.NET Core zapewnia wbudowany kontener serwisowy, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="05561-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="05561-408">Usługi są rejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="05561-409">*Wtrysk* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="05561-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="05561-410">Struktura bierze na siebie odpowiedzialność tworzenia wystąpienia zależności i usuwania go, gdy nie jest już potrzebne.</span><span class="sxs-lookup"><span data-stu-id="05561-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="05561-411">W [przykładowej](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)aplikacji `IMyDependency` interfejs definiuje metodę, którą usługa zapewnia aplikacji:</span><span class="sxs-lookup"><span data-stu-id="05561-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="05561-412">Ten interfejs jest realizowany przez `MyDependency`konkretny typ:</span><span class="sxs-lookup"><span data-stu-id="05561-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="05561-413">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w jego konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="05561-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="05561-414">Nie jest niczym niezwykłym, aby użyć iniekcji zależności w sposób łańcuchowy.</span><span class="sxs-lookup"><span data-stu-id="05561-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="05561-415">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="05561-416">Kontener rozpoznaje zależności na wykresie i zwraca w pełni rozwiązaną usługę.</span><span class="sxs-lookup"><span data-stu-id="05561-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="05561-417">Zbiorczy zestaw zależności, które muszą zostać rozwiązane, jest zazwyczaj określany jako *drzewo zależności,* *wykres zależności*lub *wykres obiektu.*</span><span class="sxs-lookup"><span data-stu-id="05561-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="05561-418">`IMyDependency`i `ILogger<TCategoryName>` muszą być zarejestrowane w kontenerze serwisowym.</span><span class="sxs-lookup"><span data-stu-id="05561-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="05561-419">`IMyDependency`jest zarejestrowana w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="05561-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05561-420">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [usługa świadczona przez framework](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="05561-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="05561-421">Kontener jest `ILogger<TCategoryName>` rozwiązywany, korzystając z [(ogólnych) otwartych typów,](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)eliminując konieczność rejestrowania każdego [(ogólnego) typu skonstruowanego:](/dotnet/csharp/language-reference/language-specification/types#constructed-types)</span><span class="sxs-lookup"><span data-stu-id="05561-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="05561-422">W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana `MyDependency`z typem betonu .</span><span class="sxs-lookup"><span data-stu-id="05561-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="05561-423">Zakresy rejestracji okres istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="05561-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="05561-424">[Okresy istnienia usługi](#service-lifetimes) są opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="05561-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="05561-425">Każda `services.Add{SERVICE_NAME}` metoda rozszerzenia dodaje (i potencjalnie konfiguruje) usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="05561-426">Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC wymagają.</span><span class="sxs-lookup"><span data-stu-id="05561-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="05561-427">Zaleca się, aby aplikacje postępowali zgodnie z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="05561-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="05561-428">Umieść metody rozszerzenia w obszarze nazw [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="05561-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="05561-429">Jeśli konstruktor usługi wymaga [wbudowanego typu,](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`takiego jak , typ może zostać wstrzyknięty za pomocą [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji:](xref:fundamentals/configuration/options)</span><span class="sxs-lookup"><span data-stu-id="05561-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="05561-430">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której usługa jest używana i przypisana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="05561-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="05561-431">Pole służy do uzyskiwania dostępu do usługi w razie potrzeby w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="05561-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="05561-432">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane `WriteMessage` do wywoływania metody usługi:</span><span class="sxs-lookup"><span data-stu-id="05561-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="05561-433">Usługi wstrzyknięte do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="05561-433">Services injected into Startup</span></span>

<span data-ttu-id="05561-434">Tylko następujące typy usług mogą `Startup` być wstrzykiwane do konstruktora podczas korzystania z hosta ogólnego (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="05561-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="05561-435">Usługi można wstrzyknąć do: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="05561-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="05561-436">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="05561-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="05561-437">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="05561-437">Framework-provided services</span></span>

<span data-ttu-id="05561-438">Metoda `Startup.ConfigureServices` jest odpowiedzialna za definiowanie usług, które aplikacja używa, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="05561-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="05561-439">Początkowo `IServiceCollection` dostarczone ma `ConfigureServices` usługi zdefiniowane przez strukturę w zależności od sposobu [hosta został skonfigurowany](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="05561-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="05561-440">Nie jest rzadkością dla aplikacji na podstawie szablonu core ASP.NET mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="05561-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="05561-441">W poniższej tabeli wymieniono niewielką próbkę usług zarejestrowanych w ramach.</span><span class="sxs-lookup"><span data-stu-id="05561-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="05561-442">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="05561-442">Service Type</span></span> | <span data-ttu-id="05561-443">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="05561-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="05561-444">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="05561-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="05561-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="05561-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="05561-448">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="05561-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="05561-450">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="05561-451">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="05561-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="05561-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="05561-454">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="05561-455">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="05561-456">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="05561-457">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="05561-458">Rejestrowanie dodatkowych usług przy pomocy metod rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="05561-458">Register additional services with extension methods</span></span>

<span data-ttu-id="05561-459">Gdy metoda rozszerzenia kolekcji usług jest dostępna do zarejestrowania usługi (i jej usług `Add{SERVICE_NAME}` zależnych, jeśli jest to wymagane), konwencja jest użycie metody pojedynczego rozszerzenia, aby zarejestrować wszystkie usługi wymagane przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="05561-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="05561-460">Poniższy kod jest przykładem dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzenia [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="05561-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="05561-461">Aby uzyskać więcej <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> informacji, zobacz klasę w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05561-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="05561-462">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="05561-462">Service lifetimes</span></span>

<span data-ttu-id="05561-463">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="05561-464">ASP.NET usługi Core można skonfigurować z następującymi okresami istnienia:</span><span class="sxs-lookup"><span data-stu-id="05561-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="05561-465">Przejściowe</span><span class="sxs-lookup"><span data-stu-id="05561-465">Transient</span></span>

<span data-ttu-id="05561-466">Usługi przejściowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>lifetime ( ) są tworzone za każdym razem, gdy są wymagane z kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="05561-467">Ten okres istnienia działa najlepiej w przypadku lekkich, bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="05561-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="05561-468">Zakresu</span><span class="sxs-lookup"><span data-stu-id="05561-468">Scoped</span></span>

<span data-ttu-id="05561-469">Usługi okresowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>o określonym zakresie ( ) są tworzone raz na żądanie klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="05561-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="05561-470">Podczas korzystania z usługi o określonym zakresie w `Invoke` oprogramowaniu pośredniczącym, wstrzyknąć usługę do lub `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="05561-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="05561-471">Nie wstrzyknąć za pomocą [iniekcji konstruktora,](xref:mvc/controllers/dependency-injection#constructor-injection) ponieważ wymusza usługę zachowywać się jak singleton.</span><span class="sxs-lookup"><span data-stu-id="05561-471">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="05561-472">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="05561-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="05561-473">Singleton</span><span class="sxs-lookup"><span data-stu-id="05561-473">Singleton</span></span>

<span data-ttu-id="05561-474">Usługi okresu istnienia<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) są tworzone po `Startup.ConfigureServices` raz pierwszy są wymagane (lub po uruchomieniu i wystąpienie jest określony z rejestracją usługi).</span><span class="sxs-lookup"><span data-stu-id="05561-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="05561-475">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="05561-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="05561-476">Jeśli aplikacja wymaga zachowania singleton, zaleca się zezwalanie kontenerowi usługi na zarządzanie okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="05561-477">Nie implementuj wzorzec projektu singleton i podaj kod użytkownika do zarządzania okres istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="05561-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="05561-478">Jest to niebezpieczne, aby rozwiązać usługi o określonym zakresie z singleton.</span><span class="sxs-lookup"><span data-stu-id="05561-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="05561-479">Może to spowodować, że usługa ma niepoprawny stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="05561-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="05561-480">Metody rejestracji usługi</span><span class="sxs-lookup"><span data-stu-id="05561-480">Service registration methods</span></span>

<span data-ttu-id="05561-481">Metody rozszerzenia rejestracji usługi oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="05561-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="05561-482">Metoda</span><span class="sxs-lookup"><span data-stu-id="05561-482">Method</span></span> | <span data-ttu-id="05561-483">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="05561-483">Automatic</span></span><br><span data-ttu-id="05561-484">obiekt</span><span class="sxs-lookup"><span data-stu-id="05561-484">object</span></span><br><span data-ttu-id="05561-485">Dyspozycji</span><span class="sxs-lookup"><span data-stu-id="05561-485">disposal</span></span> | <span data-ttu-id="05561-486">Wiele</span><span class="sxs-lookup"><span data-stu-id="05561-486">Multiple</span></span><br><span data-ttu-id="05561-487">implementacje</span><span class="sxs-lookup"><span data-stu-id="05561-487">implementations</span></span> | <span data-ttu-id="05561-488">Przekaż args</span><span class="sxs-lookup"><span data-stu-id="05561-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="05561-489">Przykład:</span><span class="sxs-lookup"><span data-stu-id="05561-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="05561-490">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-490">Yes</span></span> | <span data-ttu-id="05561-491">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-491">Yes</span></span> | <span data-ttu-id="05561-492">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="05561-493">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="05561-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="05561-494">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-494">Yes</span></span> | <span data-ttu-id="05561-495">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-495">Yes</span></span> | <span data-ttu-id="05561-496">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="05561-497">Przykład:</span><span class="sxs-lookup"><span data-stu-id="05561-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="05561-498">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-498">Yes</span></span> | <span data-ttu-id="05561-499">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-499">No</span></span> | <span data-ttu-id="05561-500">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="05561-501">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="05561-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="05561-502">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-502">No</span></span> | <span data-ttu-id="05561-503">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-503">Yes</span></span> | <span data-ttu-id="05561-504">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="05561-505">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="05561-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="05561-506">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-506">No</span></span> | <span data-ttu-id="05561-507">Nie</span><span class="sxs-lookup"><span data-stu-id="05561-507">No</span></span> | <span data-ttu-id="05561-508">Tak</span><span class="sxs-lookup"><span data-stu-id="05561-508">Yes</span></span> |

<span data-ttu-id="05561-509">Aby uzyskać więcej informacji na temat usuwania typu, zobacz [usuwanie usług](#disposal-of-services) sekcji.</span><span class="sxs-lookup"><span data-stu-id="05561-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="05561-510">Typowym scenariuszem dla wielu implementacji jest [szydercze typy do testowania.](xref:test/integration-tests#inject-mock-services)</span><span class="sxs-lookup"><span data-stu-id="05561-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="05561-511">`TryAdd{LIFETIME}`metody zarejestrować usługę tylko wtedy, gdy nie ma jeszcze zarejestrowanej implementacji.</span><span class="sxs-lookup"><span data-stu-id="05561-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="05561-512">W poniższym przykładzie pierwszy `MyDependency` wiersz `IMyDependency`rejestruje się dla .</span><span class="sxs-lookup"><span data-stu-id="05561-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="05561-513">Drugi wiersz nie ma `IMyDependency` wpływu, ponieważ ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="05561-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="05561-514">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="05561-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="05561-515">[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie ma jeszcze implementacji *tego samego typu.*</span><span class="sxs-lookup"><span data-stu-id="05561-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="05561-516">Wiele usług jest `IEnumerable<{SERVICE}>`rozwiązywanych za pośrednictwem pliku .</span><span class="sxs-lookup"><span data-stu-id="05561-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="05561-517">Podczas rejestrowania usług deweloper chce dodać wystąpienie tylko wtedy, gdy jeden z tego samego typu nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="05561-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="05561-518">Ogólnie rzecz biorąc ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="05561-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="05561-519">W poniższym przykładzie pierwszy `MyDep` wiersz `IMyDep1`rejestruje się dla .</span><span class="sxs-lookup"><span data-stu-id="05561-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="05561-520">Druga linia rejestruje `MyDep` `IMyDep2`się dla .</span><span class="sxs-lookup"><span data-stu-id="05561-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="05561-521">Trzecia linia nie ma `IMyDep1` wpływu, ponieważ `MyDep`ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="05561-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="05561-522">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="05561-522">Constructor injection behavior</span></span>

<span data-ttu-id="05561-523">Usługi można rozwiązać za pomocą dwóch mechanizmów:</span><span class="sxs-lookup"><span data-stu-id="05561-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="05561-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="05561-525">`ActivatorUtilities`jest używany z abstrakcjami skierowanymi do użytkownika, takimi jak Pomocników tagów, kontrolery MVC i spinacze modelu.</span><span class="sxs-lookup"><span data-stu-id="05561-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="05561-526">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="05561-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="05561-527">Gdy usługi są `IServiceProvider` rozpoznawane przez lub `ActivatorUtilities` [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego.*</span><span class="sxs-lookup"><span data-stu-id="05561-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="05561-528">Gdy usługi są `ActivatorUtilities`rozpoznawane przez [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, że istnieje tylko jeden odpowiedni konstruktor.</span><span class="sxs-lookup"><span data-stu-id="05561-528">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="05561-529">Przeciążenia konstruktora są obsługiwane, ale tylko jedno przeciążenie może istnieć, których argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="05561-530">Konteksty struktury encji</span><span class="sxs-lookup"><span data-stu-id="05561-530">Entity Framework contexts</span></span>

<span data-ttu-id="05561-531">Konteksty entity framework są zwykle dodawane do kontenera usługi przy użyciu [okresu istnienia o określonym zakresie,](#service-lifetimes) ponieważ operacje bazy danych aplikacji sieci web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="05561-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="05561-532">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) przeciążenie podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="05561-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="05561-533">Usługi danego okresu istnienia nie należy używać kontekstu bazy danych o krótszym okresie istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="05561-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="05561-534">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="05561-534">Lifetime and registration options</span></span>

<span data-ttu-id="05561-535">Aby zademonstrować różnicę między opcjami okresu istnienia i rejestracji, należy wziąć pod uwagę `OperationId`następujące interfejsy, które reprezentują zadania jako operację z unikatowym identyfikatorem .</span><span class="sxs-lookup"><span data-stu-id="05561-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="05561-536">W zależności od tego, jak okres istnienia usługi operacyjnej jest skonfigurowany dla następujących interfejsów, kontener udostępnia takie samo lub inne wystąpienie usługi, gdy jest to wymagane przez klasę:</span><span class="sxs-lookup"><span data-stu-id="05561-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="05561-537">Interfejsy są implementowane `Operation` w klasie.</span><span class="sxs-lookup"><span data-stu-id="05561-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="05561-538">Konstruktor `Operation` generuje identyfikator GUID, jeśli nie jest dostarczany:</span><span class="sxs-lookup"><span data-stu-id="05561-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="05561-539">An `OperationService` jest zarejestrowany, który zależy `Operation` od każdego z innych typów.</span><span class="sxs-lookup"><span data-stu-id="05561-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="05561-540">Gdy `OperationService` jest wymagane za pośrednictwem iniekcji zależności, odbiera nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="05561-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="05561-541">Gdy usługi przejściowe są tworzone na żądanie `OperationId` z `IOperationTransient` kontenera, `OperationId` usługa `OperationService`jest inna niż z .</span><span class="sxs-lookup"><span data-stu-id="05561-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="05561-542">`OperationService`otrzymuje nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="05561-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="05561-543">Nowe wystąpienie daje `OperationId`inny .</span><span class="sxs-lookup"><span data-stu-id="05561-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="05561-544">Gdy usługi o określonym zakresie są `OperationId` tworzone `IOperationScoped` na żądanie klienta, `OperationService` usługa jest taka sama jak w żądaniu klienta.</span><span class="sxs-lookup"><span data-stu-id="05561-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="05561-545">W przypadku żądań klientów `OperationId` obie usługi mają inną wartość.</span><span class="sxs-lookup"><span data-stu-id="05561-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="05561-546">Gdy usługi pojedynczego i pojedynczego wystąpienia są tworzone raz i używane `OperationId` we wszystkich żądaniach klientów i wszystkich usługach, jest stała we wszystkich żądaniach usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="05561-547">W `Startup.ConfigureServices`, każdy typ jest dodawany do kontenera zgodnie z jego nazwany okres istnienia:</span><span class="sxs-lookup"><span data-stu-id="05561-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="05561-548">Usługa `IOperationSingletonInstance` używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="05561-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="05561-549">Jest jasne, gdy ten typ jest w użyciu (jego identyfikator GUID jest wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="05561-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="05561-550">Przykładowa aplikacja pokazuje okres istnienia obiektu w obrębie i między poszczególnymi żądaniami.</span><span class="sxs-lookup"><span data-stu-id="05561-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="05561-551">Przykładowa aplikacja `IndexModel` żąda każdego `IOperation` rodzaju typu `OperationService`i .</span><span class="sxs-lookup"><span data-stu-id="05561-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="05561-552">Następnie strona wyświetla wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="05561-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="05561-553">Dwa następujące dane wyjściowe pokazuje wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="05561-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="05561-554">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="05561-554">**First request:**</span></span>

<span data-ttu-id="05561-555">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="05561-555">Controller operations:</span></span>

<span data-ttu-id="05561-556">Przemijające: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="05561-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="05561-557">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="05561-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="05561-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-559">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-560">`OperationService`Operacji:</span><span class="sxs-lookup"><span data-stu-id="05561-560">`OperationService` operations:</span></span>

<span data-ttu-id="05561-561">Przemijające: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="05561-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="05561-562">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="05561-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="05561-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-564">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-565">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="05561-565">**Second request:**</span></span>

<span data-ttu-id="05561-566">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="05561-566">Controller operations:</span></span>

<span data-ttu-id="05561-567">Przemijające: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="05561-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="05561-568">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="05561-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="05561-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-570">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-571">`OperationService`Operacji:</span><span class="sxs-lookup"><span data-stu-id="05561-571">`OperationService` operations:</span></span>

<span data-ttu-id="05561-572">Przejściowe: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="05561-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="05561-573">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="05561-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="05561-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05561-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05561-575">Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000</span><span class="sxs-lookup"><span data-stu-id="05561-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05561-576">Należy przestrzegać, `OperationId` które z wartości różnią się w obrębie żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="05561-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="05561-577">*Obiekty przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="05561-577">*Transient* objects are always different.</span></span> <span data-ttu-id="05561-578">Wartość przejściowa `OperationId` dla żądań pierwszego i drugiego `OperationService` klienta są różne dla operacji i żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="05561-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="05561-579">Nowe wystąpienie jest dostarczane do każdego żądania usługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="05561-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="05561-580">*Obiekty o określonym zakresie* są takie same w żądaniu klienta, ale różne w żądaniach klientów.</span><span class="sxs-lookup"><span data-stu-id="05561-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="05561-581">*Singleton* obiekty są takie same dla każdego obiektu `Operation` i każdego `Startup.ConfigureServices`żądania, niezależnie od tego, czy wystąpienie jest przewidziane w .</span><span class="sxs-lookup"><span data-stu-id="05561-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="05561-582">Zadzwoń do usług z głównych</span><span class="sxs-lookup"><span data-stu-id="05561-582">Call services from main</span></span>

<span data-ttu-id="05561-583">Utwórz <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> za pomocą [IServiceScopeFactory.CreateScope,](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) aby rozwiązać usługi o określonym zakresie w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="05561-584">Takie podejście jest przydatne do uzyskania dostępu do usługi o określonym zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="05561-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="05561-585">W poniższym przykładzie pokazano, `MyScopedService` jak `Program.Main`uzyskać kontekst dla w:</span><span class="sxs-lookup"><span data-stu-id="05561-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="05561-586">Sprawdzanie poprawności zakresu</span><span class="sxs-lookup"><span data-stu-id="05561-586">Scope validation</span></span>

<span data-ttu-id="05561-587">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług wykonuje kontrole, aby sprawdzić, czy:</span><span class="sxs-lookup"><span data-stu-id="05561-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="05561-588">Usługi o określonym zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez głównego dostawcę usług.</span><span class="sxs-lookup"><span data-stu-id="05561-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="05561-589">Usługi o określonym zakresie nie są bezpośrednio lub pośrednio wstrzykuje się do singletons.</span><span class="sxs-lookup"><span data-stu-id="05561-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="05561-590">Główny dostawca usług jest <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> tworzony, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="05561-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="05561-591">Okres istnienia głównego dostawcy usług odpowiada okresowi istnienia aplikacji/serwera, gdy dostawca uruchamia się z aplikacją i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="05561-592">Usługi o określonym zakresie są usuwane przez kontener, który je utworzył.</span><span class="sxs-lookup"><span data-stu-id="05561-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="05561-593">Jeśli usługa o określonym zakresie jest tworzona w kontenerze głównym, okres istnienia usługi jest skutecznie promowany do singleton, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest zamknięty.</span><span class="sxs-lookup"><span data-stu-id="05561-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="05561-594">Sprawdzanie poprawności zakresów usługi łapie te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="05561-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="05561-595">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="05561-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="05561-596">Żądania usług</span><span class="sxs-lookup"><span data-stu-id="05561-596">Request Services</span></span>

<span data-ttu-id="05561-597">Usługi dostępne w ramach żądania core `HttpContext` ASP.NET są udostępniane za pośrednictwem [httpContext.RequestServices kolekcji.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)</span><span class="sxs-lookup"><span data-stu-id="05561-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="05561-598">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05561-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="05561-599">Gdy obiekty określają zależności, są one spełnione `RequestServices`przez `ApplicationServices`typy znalezione w , nie .</span><span class="sxs-lookup"><span data-stu-id="05561-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="05561-600">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="05561-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="05561-601">Zamiast tego zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas i zezwalaj na platformę wstrzyknąć zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="05561-602">Daje to klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="05561-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="05561-603">Preferuj żądanie zależności jako parametry konstruktora dostępu do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="05561-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="05561-604">Usługi projektowe do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="05561-604">Design services for dependency injection</span></span>

<span data-ttu-id="05561-605">Najważniejsze wskazówki to:</span><span class="sxs-lookup"><span data-stu-id="05561-605">Best practices are to:</span></span>

* <span data-ttu-id="05561-606">Projektowanie usług do korzystania z iniekcji zależności, aby uzyskać ich zależności.</span><span class="sxs-lookup"><span data-stu-id="05561-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="05561-607">Unikaj klas stanowych, statycznych i członków.</span><span class="sxs-lookup"><span data-stu-id="05561-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="05561-608">Projektowanie aplikacji do korzystania z usług singleton zamiast tego, które unikają tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="05561-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="05561-609">Unikaj bezpośredniego tworzenia wystąpienia klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="05561-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="05561-610">Bezpośrednie tworzenie wystąpienia łączy kod z określoną implementacją.</span><span class="sxs-lookup"><span data-stu-id="05561-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="05561-611">Spraw, aby klasy aplikacji były małe, dobrze uwzględnione i łatwe do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="05561-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="05561-612">Jeśli klasa wydaje się mieć zbyt wiele wstrzykniętych zależności, jest to zazwyczaj znak, że klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="05561-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="05561-613">Spróbuj refaktoryzowania klasy, przenosząc niektóre z jego obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="05561-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="05561-614">Należy pamiętać, że klasy modelu strony Razor Pages i klasy kontrolera MVC powinny koncentrować się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="05561-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="05561-615">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [odrębnych problemów.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)</span><span class="sxs-lookup"><span data-stu-id="05561-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="05561-616">Unieszkodliwianie usług</span><span class="sxs-lookup"><span data-stu-id="05561-616">Disposal of services</span></span>

<span data-ttu-id="05561-617">Kontener wymaga <xref:System.IDisposable.Dispose*> typów, <xref:System.IDisposable> które tworzy.</span><span class="sxs-lookup"><span data-stu-id="05561-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="05561-618">Jeśli wystąpienie zostanie dodane do kontenera przez kod użytkownika, nie jest usuwany automatycznie.</span><span class="sxs-lookup"><span data-stu-id="05561-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="05561-619">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="05561-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="05561-620">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="05561-620">In the following example:</span></span>

* <span data-ttu-id="05561-621">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="05561-622">Okres istnienia zamierzonej usługi nie są znane przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="05561-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="05561-623">Struktura nie usuwa usług automatycznie.</span><span class="sxs-lookup"><span data-stu-id="05561-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="05561-624">Jeśli usługi nie są jawnie usuwane w kodzie dewelopera, utrzymują się, dopóki aplikacja nie zostanie zamknięta.</span><span class="sxs-lookup"><span data-stu-id="05561-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="05561-625">Domyślna wymiana kontenera usługi</span><span class="sxs-lookup"><span data-stu-id="05561-625">Default service container replacement</span></span>

<span data-ttu-id="05561-626">Wbudowany kontener usługi został zaprojektowany do obsługi potrzeb struktury i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="05561-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="05561-627">Zaleca się użycie wbudowanego kontenera, chyba że potrzebna jest określona funkcja, której wbudowany kontener nie obsługuje, na przykład:</span><span class="sxs-lookup"><span data-stu-id="05561-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="05561-628">Wstrzyknięcie właściwości</span><span class="sxs-lookup"><span data-stu-id="05561-628">Property injection</span></span>
* <span data-ttu-id="05561-629">Wstrzyknięcie na podstawie nazwy</span><span class="sxs-lookup"><span data-stu-id="05561-629">Injection based on name</span></span>
* <span data-ttu-id="05561-630">Pojemniki dla dzieci</span><span class="sxs-lookup"><span data-stu-id="05561-630">Child containers</span></span>
* <span data-ttu-id="05561-631">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="05561-631">Custom lifetime management</span></span>
* <span data-ttu-id="05561-632">`Func<T>`obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="05561-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="05561-633">Rejestracja oparta na konwencji</span><span class="sxs-lookup"><span data-stu-id="05561-633">Convention-based registration</span></span>

<span data-ttu-id="05561-634">Z ASP.NET aplikacjami Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="05561-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="05561-635">Autofac (autofac)</span><span class="sxs-lookup"><span data-stu-id="05561-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="05561-636">Suchość</span><span class="sxs-lookup"><span data-stu-id="05561-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="05561-637">Grace</span><span class="sxs-lookup"><span data-stu-id="05561-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="05561-638">Lekka Inject</span><span class="sxs-lookup"><span data-stu-id="05561-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="05561-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="05561-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="05561-640">Skrytka</span><span class="sxs-lookup"><span data-stu-id="05561-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="05561-641">Unity</span><span class="sxs-lookup"><span data-stu-id="05561-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="05561-642">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="05561-642">Thread safety</span></span>

<span data-ttu-id="05561-643">Tworzenie usług singleton bezpiecznych dla wątków.</span><span class="sxs-lookup"><span data-stu-id="05561-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="05561-644">Jeśli usługa singleton ma zależność od usługi przejściowej, usługi przejściowe mogą również wymagać bezpieczeństwa wątku w zależności od tego, jak jest używany przez singleton.</span><span class="sxs-lookup"><span data-stu-id="05561-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="05561-645">Metoda fabryczna pojedynczej usługi, takich jak drugi argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczne dla wątków.</span><span class="sxs-lookup"><span data-stu-id="05561-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="05561-646">Podobnie jak`static`konstruktor typu ( ), jest gwarantowane, aby być wywoływane raz przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="05561-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="05561-647">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="05561-647">Recommendations</span></span>

* <span data-ttu-id="05561-648">`async/await`i `Task` rozdzielczość usługi nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="05561-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="05561-649">C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec jest użycie metod asynchronicznych po synchronicznie rozwiązywania usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="05561-650">Należy unikać przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="05561-651">Na przykład koszyk użytkownika zazwyczaj nie powinny być dodawane do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="05561-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="05561-652">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="05561-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="05561-653">Podobnie należy unikać "posiadacza danych" obiektów, które istnieją tylko w celu umożliwienia dostępu do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="05561-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="05561-654">Lepiej jest poprosić o rzeczywisty element za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="05561-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="05561-655">Unikaj statycznego dostępu do usług (na przykład statycznie wpisując [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użycia w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="05561-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="05561-656">Należy unikać używania *wzorca lokalizatora usług,* który łączy [inwersję](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategii kontroli.</span><span class="sxs-lookup"><span data-stu-id="05561-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="05561-657">Nie wywoływać, <xref:System.IServiceProvider.GetService*> aby uzyskać wystąpienie usługi, gdy można użyć DI zamiast:</span><span class="sxs-lookup"><span data-stu-id="05561-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="05561-658">**Niepoprawne:**</span><span class="sxs-lookup"><span data-stu-id="05561-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="05561-659">**Prawidłowe:**</span><span class="sxs-lookup"><span data-stu-id="05561-659">**Correct**:</span></span>

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

  * <span data-ttu-id="05561-660">Należy unikać wstrzykiwania fabryki, która rozwiązuje <xref:System.IServiceProvider.GetService*>zależności w czasie wykonywania przy użyciu .</span><span class="sxs-lookup"><span data-stu-id="05561-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="05561-661">Unikaj dostępu statycznego (na `HttpContext` przykład [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="05561-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="05561-662">Podobnie jak wszystkie zestawy zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="05561-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="05561-663">Wyjątki są&mdash;rzadkie głównie szczególnych przypadkach w samych ramach.</span><span class="sxs-lookup"><span data-stu-id="05561-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="05561-664">DI jest *alternatywą dla* wzorców dostępu do obiektów statycznych/globalnych.</span><span class="sxs-lookup"><span data-stu-id="05561-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="05561-665">Możesz nie być w stanie zrealizować korzyści z DI, jeśli mieszasz go z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="05561-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05561-666">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="05561-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="05561-667">Pisanie czystego kodu w ASP.NET Core z iniekcji zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="05561-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="05561-668">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="05561-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="05561-669">Odwrócenie kontenerów kontrolnych i wzorzec iniekcji zależności (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="05561-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="05561-670">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="05561-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
