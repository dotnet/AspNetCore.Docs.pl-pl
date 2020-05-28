---
<span data-ttu-id="d792b-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-102">'Blazor'</span></span>
- <span data-ttu-id="d792b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-103">'Identity'</span></span>
- <span data-ttu-id="d792b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-105">'Razor'</span></span>
- <span data-ttu-id="d792b-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="d792b-107">Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d792b-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="d792b-108">[Steve Kowalski](https://ardalis.com/) i [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="d792b-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d792b-109">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="d792b-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d792b-110">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="d792b-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d792b-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d792b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d792b-112">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="d792b-112">Overview of dependency injection</span></span>

<span data-ttu-id="d792b-113">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="d792b-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d792b-114">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d792b-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="d792b-115">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d792b-116">`MyDependency`Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="d792b-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d792b-117">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d792b-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d792b-118">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="d792b-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d792b-119">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="d792b-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d792b-120">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="d792b-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d792b-121">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d792b-122">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="d792b-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d792b-123">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="d792b-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d792b-124">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="d792b-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d792b-125">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d792b-126">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d792b-127">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d792b-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d792b-128">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d792b-129">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="d792b-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d792b-130">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="d792b-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d792b-131">W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d792b-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d792b-132">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d792b-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d792b-133">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="d792b-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d792b-134">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="d792b-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d792b-135">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d792b-136">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d792b-137">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="d792b-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="d792b-138">`IMyDependency`i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d792b-139">`IMyDependency`jest zarejestrowany w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d792b-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d792b-140">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="d792b-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d792b-141">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="d792b-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="d792b-142">W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d792b-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d792b-143">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="d792b-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d792b-144">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="d792b-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d792b-145">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="d792b-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d792b-146">Na przykład `services.AddMvc()` dodaje Razor strony usług i wymagane przez MVC.</span><span class="sxs-lookup"><span data-stu-id="d792b-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="d792b-147">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="d792b-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d792b-148">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d792b-149">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string` ,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="d792b-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="d792b-150">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="d792b-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d792b-151">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d792b-152">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="d792b-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="d792b-153">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="d792b-153">Services injected into Startup</span></span>

<span data-ttu-id="d792b-154">Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="d792b-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d792b-155">Usługi można wstrzyknąć do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d792b-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d792b-156">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d792b-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d792b-157">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="d792b-157">Framework-provided services</span></span>

<span data-ttu-id="d792b-158">`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d792b-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d792b-159">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d792b-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d792b-160">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="d792b-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d792b-161">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="d792b-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d792b-162">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="d792b-162">Service Type</span></span> | <span data-ttu-id="d792b-163">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="d792b-163">Lifetime</span></span> |
| ---
<span data-ttu-id="d792b-164">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-165">'Blazor'</span></span>
- <span data-ttu-id="d792b-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-166">'Identity'</span></span>
- <span data-ttu-id="d792b-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-168">'Razor'</span></span>
- <span data-ttu-id="d792b-169">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-170">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-171">'Blazor'</span></span>
- <span data-ttu-id="d792b-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-172">'Identity'</span></span>
- <span data-ttu-id="d792b-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-174">'Razor'</span></span>
- <span data-ttu-id="d792b-175">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-176">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-177">'Blazor'</span></span>
- <span data-ttu-id="d792b-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-178">'Identity'</span></span>
- <span data-ttu-id="d792b-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-180">'Razor'</span></span>
- <span data-ttu-id="d792b-181">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-182">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-183">'Blazor'</span></span>
- <span data-ttu-id="d792b-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-184">'Identity'</span></span>
- <span data-ttu-id="d792b-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-186">'Razor'</span></span>
- <span data-ttu-id="d792b-187">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-187">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-188">------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-189">'Blazor'</span></span>
- <span data-ttu-id="d792b-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-190">'Identity'</span></span>
- <span data-ttu-id="d792b-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-192">'Razor'</span></span>
- <span data-ttu-id="d792b-193">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-194">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-195">'Blazor'</span></span>
- <span data-ttu-id="d792b-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-196">'Identity'</span></span>
- <span data-ttu-id="d792b-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-198">'Razor'</span></span>
- <span data-ttu-id="d792b-199">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-199">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Przejściowe | | `IHostApplicationLifetime` | Pojedyncze | | `IWebHostEnvironment` | Pojedyncze | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Przejściowe | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Przejściowe | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Przejściowe | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Pojedyncze | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Pojedyncze | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Pojedyncze |</span><span class="sxs-lookup"><span data-stu-id="d792b-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d792b-201">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="d792b-201">Register additional services with extension methods</span></span>

<span data-ttu-id="d792b-202">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać pojedynczej `Add{SERVICE_NAME}` metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="d792b-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d792b-203">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="d792b-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="d792b-204">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="d792b-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d792b-205">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="d792b-205">Service lifetimes</span></span>

<span data-ttu-id="d792b-206">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d792b-207">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="d792b-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d792b-208">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="d792b-208">Transient</span></span>

<span data-ttu-id="d792b-209">Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d792b-210">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="d792b-211">Zakresie</span><span class="sxs-lookup"><span data-stu-id="d792b-211">Scoped</span></span>

<span data-ttu-id="d792b-212">Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="d792b-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="d792b-213">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` `InvokeAsync` metody lub.</span><span class="sxs-lookup"><span data-stu-id="d792b-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d792b-214">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="d792b-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d792b-215">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d792b-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d792b-216">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="d792b-216">Singleton</span></span>

<span data-ttu-id="d792b-217">Pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="d792b-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d792b-218">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d792b-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d792b-219">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d792b-220">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="d792b-221">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="d792b-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d792b-222">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="d792b-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d792b-223">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="d792b-223">Service registration methods</span></span>

<span data-ttu-id="d792b-224">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="d792b-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d792b-225">Metoda</span><span class="sxs-lookup"><span data-stu-id="d792b-225">Method</span></span> | <span data-ttu-id="d792b-226">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="d792b-226">Automatic</span></span><br><span data-ttu-id="d792b-227">object</span><span class="sxs-lookup"><span data-stu-id="d792b-227">object</span></span><br><span data-ttu-id="d792b-228">myśl</span><span class="sxs-lookup"><span data-stu-id="d792b-228">disposal</span></span> | <span data-ttu-id="d792b-229">Wiele</span><span class="sxs-lookup"><span data-stu-id="d792b-229">Multiple</span></span><br><span data-ttu-id="d792b-230">implementacje</span><span class="sxs-lookup"><span data-stu-id="d792b-230">implementations</span></span> | <span data-ttu-id="d792b-231">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="d792b-231">Pass args</span></span> |
| ---
<span data-ttu-id="d792b-232">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-233">'Blazor'</span></span>
- <span data-ttu-id="d792b-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-234">'Identity'</span></span>
- <span data-ttu-id="d792b-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-236">'Razor'</span></span>
- <span data-ttu-id="d792b-237">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-237">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-238">--- | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-239">'Blazor'</span></span>
- <span data-ttu-id="d792b-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-240">'Identity'</span></span>
- <span data-ttu-id="d792b-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-242">'Razor'</span></span>
- <span data-ttu-id="d792b-243">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-244">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-245">'Blazor'</span></span>
- <span data-ttu-id="d792b-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-246">'Identity'</span></span>
- <span data-ttu-id="d792b-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-248">'Razor'</span></span>
- <span data-ttu-id="d792b-249">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-250">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-251">'Blazor'</span></span>
- <span data-ttu-id="d792b-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-252">'Identity'</span></span>
- <span data-ttu-id="d792b-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-254">'Razor'</span></span>
- <span data-ttu-id="d792b-255">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-256">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-257">'Blazor'</span></span>
- <span data-ttu-id="d792b-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-258">'Identity'</span></span>
- <span data-ttu-id="d792b-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-260">'Razor'</span></span>
- <span data-ttu-id="d792b-261">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-262">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-263">'Blazor'</span></span>
- <span data-ttu-id="d792b-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-264">'Identity'</span></span>
- <span data-ttu-id="d792b-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-266">'Razor'</span></span>
- <span data-ttu-id="d792b-267">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-268">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-269">'Blazor'</span></span>
- <span data-ttu-id="d792b-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-270">'Identity'</span></span>
- <span data-ttu-id="d792b-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-272">'Razor'</span></span>
- <span data-ttu-id="d792b-273">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-274">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-275">'Blazor'</span></span>
- <span data-ttu-id="d792b-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-276">'Identity'</span></span>
- <span data-ttu-id="d792b-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-278">'Razor'</span></span>
- <span data-ttu-id="d792b-279">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-280">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-281">'Blazor'</span></span>
- <span data-ttu-id="d792b-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-282">'Identity'</span></span>
- <span data-ttu-id="d792b-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-284">'Razor'</span></span>
- <span data-ttu-id="d792b-285">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-286">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-287">'Blazor'</span></span>
- <span data-ttu-id="d792b-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-288">'Identity'</span></span>
- <span data-ttu-id="d792b-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-290">'Razor'</span></span>
- <span data-ttu-id="d792b-291">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-292">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-293">'Blazor'</span></span>
- <span data-ttu-id="d792b-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-294">'Identity'</span></span>
- <span data-ttu-id="d792b-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-296">'Razor'</span></span>
- <span data-ttu-id="d792b-297">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-298">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-299">'Blazor'</span></span>
- <span data-ttu-id="d792b-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-300">'Identity'</span></span>
- <span data-ttu-id="d792b-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-302">'Razor'</span></span>
- <span data-ttu-id="d792b-303">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-304">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-305">'Blazor'</span></span>
- <span data-ttu-id="d792b-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-306">'Identity'</span></span>
- <span data-ttu-id="d792b-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-308">'Razor'</span></span>
- <span data-ttu-id="d792b-309">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-309">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-310">---------------: | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-311">'Blazor'</span></span>
- <span data-ttu-id="d792b-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-312">'Identity'</span></span>
- <span data-ttu-id="d792b-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-314">'Razor'</span></span>
- <span data-ttu-id="d792b-315">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-316">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-317">'Blazor'</span></span>
- <span data-ttu-id="d792b-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-318">'Identity'</span></span>
- <span data-ttu-id="d792b-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-320">'Razor'</span></span>
- <span data-ttu-id="d792b-321">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-322">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-323">'Blazor'</span></span>
- <span data-ttu-id="d792b-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-324">'Identity'</span></span>
- <span data-ttu-id="d792b-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-326">'Razor'</span></span>
- <span data-ttu-id="d792b-327">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-328">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-329">'Blazor'</span></span>
- <span data-ttu-id="d792b-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-330">'Identity'</span></span>
- <span data-ttu-id="d792b-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-332">'Razor'</span></span>
- <span data-ttu-id="d792b-333">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-334">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-335">'Blazor'</span></span>
- <span data-ttu-id="d792b-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-336">'Identity'</span></span>
- <span data-ttu-id="d792b-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-338">'Razor'</span></span>
- <span data-ttu-id="d792b-339">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-340">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-341">'Blazor'</span></span>
- <span data-ttu-id="d792b-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-342">'Identity'</span></span>
- <span data-ttu-id="d792b-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-344">'Razor'</span></span>
- <span data-ttu-id="d792b-345">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-346">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-347">'Blazor'</span></span>
- <span data-ttu-id="d792b-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-348">'Identity'</span></span>
- <span data-ttu-id="d792b-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-350">'Razor'</span></span>
- <span data-ttu-id="d792b-351">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-352">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-353">'Blazor'</span></span>
- <span data-ttu-id="d792b-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-354">'Identity'</span></span>
- <span data-ttu-id="d792b-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-356">'Razor'</span></span>
- <span data-ttu-id="d792b-357">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-358">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-359">'Blazor'</span></span>
- <span data-ttu-id="d792b-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-360">'Identity'</span></span>
- <span data-ttu-id="d792b-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-362">'Razor'</span></span>
- <span data-ttu-id="d792b-363">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-364">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-365">'Blazor'</span></span>
- <span data-ttu-id="d792b-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-366">'Identity'</span></span>
- <span data-ttu-id="d792b-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-368">'Razor'</span></span>
- <span data-ttu-id="d792b-369">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-369">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-370">-------------: | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-371">'Blazor'</span></span>
- <span data-ttu-id="d792b-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-372">'Identity'</span></span>
- <span data-ttu-id="d792b-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-374">'Razor'</span></span>
- <span data-ttu-id="d792b-375">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-375">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="d792b-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="d792b-377">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d792b-377">Example:</span></span><br><span data-ttu-id="d792b-378">`services.AddSingleton<IMyDep, MyDep>();`| Tak | Tak | Nie | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="d792b-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="d792b-379">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="d792b-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="d792b-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Tak | Tak | Tak | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="d792b-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="d792b-381">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d792b-381">Example:</span></span><br><span data-ttu-id="d792b-382">`services.AddSingleton<MyDep>();`| Tak | Nie | Nie | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="d792b-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="d792b-383">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="d792b-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="d792b-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Nie | Tak | Tak | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="d792b-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="d792b-385">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="d792b-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="d792b-386">`services.AddSingleton(new MyDep("A string!"));`| Nie | Nie | Tak |</span><span class="sxs-lookup"><span data-stu-id="d792b-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="d792b-387">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="d792b-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d792b-388">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d792b-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d792b-389">`TryAdd{LIFETIME}`Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d792b-390">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d792b-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="d792b-391">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="d792b-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="d792b-392">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="d792b-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d792b-393">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="d792b-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d792b-394">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d792b-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d792b-395">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="d792b-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d792b-396">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="d792b-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d792b-397">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="d792b-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d792b-398">Drugi wiersz rejestruje `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="d792b-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d792b-399">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="d792b-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d792b-400">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="d792b-400">Constructor injection behavior</span></span>

<span data-ttu-id="d792b-401">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="d792b-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d792b-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d792b-403">`ActivatorUtilities`jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="d792b-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d792b-404">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="d792b-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d792b-405">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="d792b-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d792b-406">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="d792b-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d792b-407">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d792b-408">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d792b-408">Entity Framework contexts</span></span>

<span data-ttu-id="d792b-409">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="d792b-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d792b-410">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="d792b-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d792b-411">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="d792b-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d792b-412">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="d792b-412">Lifetime and registration options</span></span>

<span data-ttu-id="d792b-413">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z unikatowym identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d792b-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d792b-414">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="d792b-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d792b-415">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d792b-416">`Operation`Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="d792b-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d792b-417">`OperationService`Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="d792b-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d792b-418">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="d792b-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d792b-419">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` Usługa różni `IOperationTransient` się od `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="d792b-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d792b-420">`OperationService`odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="d792b-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d792b-421">Nowe wystąpienie daje inną wartość `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d792b-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d792b-422">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` Usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="d792b-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d792b-423">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="d792b-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d792b-424">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="d792b-425">W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="d792b-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="d792b-426">`IOperationSingletonInstance`Usługa używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="d792b-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d792b-427">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="d792b-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d792b-428">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="d792b-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d792b-429">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="d792b-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d792b-430">Następnie na stronie zostaną wyświetlone wszystkie wartości klasy modelu strony i usługi `OperationId` za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="d792b-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="d792b-431">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="d792b-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d792b-432">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="d792b-432">**First request:**</span></span>

<span data-ttu-id="d792b-433">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="d792b-433">Controller operations:</span></span>

<span data-ttu-id="d792b-434">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d792b-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d792b-435">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d792b-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d792b-436">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-437">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-438">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="d792b-438">`OperationService` operations:</span></span>

<span data-ttu-id="d792b-439">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d792b-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d792b-440">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d792b-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d792b-441">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-442">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-443">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="d792b-443">**Second request:**</span></span>

<span data-ttu-id="d792b-444">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="d792b-444">Controller operations:</span></span>

<span data-ttu-id="d792b-445">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d792b-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d792b-446">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d792b-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d792b-447">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-448">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-449">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="d792b-449">`OperationService` operations:</span></span>

<span data-ttu-id="d792b-450">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d792b-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d792b-451">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d792b-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d792b-452">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-453">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-454">Zauważ, że `OperationId` wartości różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="d792b-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d792b-455">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="d792b-455">*Transient* objects are always different.</span></span> <span data-ttu-id="d792b-456">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="d792b-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d792b-457">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="d792b-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d792b-458">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="d792b-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d792b-459">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, czy `Operation` wystąpienie jest dostarczone w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d792b-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d792b-460">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="d792b-460">Call services from main</span></span>

<span data-ttu-id="d792b-461">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d792b-462">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="d792b-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d792b-463">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d792b-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="d792b-464">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="d792b-464">Scope validation</span></span>

<span data-ttu-id="d792b-465">Gdy aplikacja jest uruchomiona w środowisku deweloperskim i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do skompilowania hosta, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="d792b-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d792b-466">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="d792b-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d792b-467">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="d792b-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d792b-468">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="d792b-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d792b-469">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d792b-470">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="d792b-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d792b-471">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="d792b-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d792b-472">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="d792b-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d792b-473">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d792b-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="d792b-474">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="d792b-474">Request Services</span></span>

<span data-ttu-id="d792b-475">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="d792b-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d792b-476">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d792b-477">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="d792b-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d792b-478">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="d792b-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d792b-479">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól platformie na wstrzyknięcie zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="d792b-480">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="d792b-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d792b-481">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="d792b-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d792b-482">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="d792b-482">Design services for dependency injection</span></span>

<span data-ttu-id="d792b-483">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="d792b-483">Best practices are to:</span></span>

* <span data-ttu-id="d792b-484">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d792b-485">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="d792b-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d792b-486">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="d792b-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d792b-487">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d792b-488">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d792b-489">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="d792b-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d792b-490">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d792b-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d792b-491">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="d792b-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d792b-492">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d792b-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d792b-493">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="d792b-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d792b-494">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="d792b-494">Disposal of services</span></span>

<span data-ttu-id="d792b-495">Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="d792b-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d792b-496">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="d792b-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="d792b-497">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="d792b-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="d792b-498">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d792b-498">In the following example:</span></span>

* <span data-ttu-id="d792b-499">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d792b-500">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="d792b-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d792b-501">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d792b-502">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="d792b-503">Aby zapoznać się z omówieniem opcji usuwania usług, zobacz [cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="d792b-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="d792b-504">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="d792b-504">Default service container replacement</span></span>

<span data-ttu-id="d792b-505">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="d792b-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d792b-506">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="d792b-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d792b-507">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="d792b-507">Property injection</span></span>
* <span data-ttu-id="d792b-508">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="d792b-508">Injection based on name</span></span>
* <span data-ttu-id="d792b-509">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="d792b-509">Child containers</span></span>
* <span data-ttu-id="d792b-510">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="d792b-510">Custom lifetime management</span></span>
* <span data-ttu-id="d792b-511">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="d792b-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d792b-512">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="d792b-512">Convention-based registration</span></span>

<span data-ttu-id="d792b-513">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="d792b-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d792b-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="d792b-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d792b-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d792b-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d792b-516">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="d792b-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d792b-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="d792b-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d792b-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="d792b-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d792b-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d792b-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d792b-520">Unity</span><span class="sxs-lookup"><span data-stu-id="d792b-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d792b-521">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="d792b-521">Thread safety</span></span>

<span data-ttu-id="d792b-522">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="d792b-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="d792b-523">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="d792b-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d792b-524">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="d792b-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d792b-525">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="d792b-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d792b-526">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="d792b-526">Recommendations</span></span>

* <span data-ttu-id="d792b-527">`async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="d792b-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d792b-528">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d792b-529">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d792b-530">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d792b-531">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d792b-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d792b-532">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="d792b-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d792b-533">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="d792b-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="d792b-534">Należy unikać statycznego dostępu do usług (na przykład statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="d792b-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="d792b-535">Unikaj używania *wzorca lokalizatora usługi*.</span><span class="sxs-lookup"><span data-stu-id="d792b-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="d792b-536">Na przykład nie wywołuj, <xref:System.IServiceProvider.GetService*> Aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="d792b-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="d792b-537">**Prawidłowy**</span><span class="sxs-lookup"><span data-stu-id="d792b-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="d792b-538">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="d792b-538">**Correct**:</span></span>

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

* <span data-ttu-id="d792b-539">Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="d792b-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="d792b-540">Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="d792b-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="d792b-541">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d792b-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d792b-542">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="d792b-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d792b-543">Wyjątki są rzadko &mdash; w większości specjalnych przypadków w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="d792b-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d792b-544">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="d792b-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d792b-545">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="d792b-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="d792b-546">Zalecane wzorce dla wielu dzierżawców w programie DI</span><span class="sxs-lookup"><span data-stu-id="d792b-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="d792b-547">[Podstawowe funkcje sadu](https://github.com/OrchardCMS/OrchardCore) zapewniają wiele dzierżawców.</span><span class="sxs-lookup"><span data-stu-id="d792b-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="d792b-548">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją podstawową programu sadu](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="d792b-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="d792b-549">Zapoznaj się z https://github.com/OrchardCMS/OrchardCore.Samples przykładowymi aplikacjami, aby poznać Przykłady sposobu tworzenia aplikacji modularnych i wielodostępnych przy użyciu samej platformy sadu Core bez żadnej z określonych funkcji usługi CMS.</span><span class="sxs-lookup"><span data-stu-id="d792b-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d792b-550">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d792b-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d792b-551">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d792b-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="d792b-552">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="d792b-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d792b-553">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="d792b-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d792b-554">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="d792b-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d792b-555">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="d792b-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d792b-556">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="d792b-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d792b-557">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d792b-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d792b-558">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="d792b-558">Overview of dependency injection</span></span>

<span data-ttu-id="d792b-559">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="d792b-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d792b-560">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d792b-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="d792b-561">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d792b-562">`MyDependency`Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="d792b-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d792b-563">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d792b-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d792b-564">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="d792b-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d792b-565">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="d792b-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d792b-566">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="d792b-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d792b-567">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d792b-568">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="d792b-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d792b-569">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="d792b-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d792b-570">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="d792b-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d792b-571">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d792b-572">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d792b-573">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="d792b-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d792b-574">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d792b-575">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="d792b-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d792b-576">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="d792b-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d792b-577">W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d792b-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="d792b-578">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="d792b-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="d792b-579">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="d792b-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d792b-580">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="d792b-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d792b-581">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d792b-582">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d792b-583">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="d792b-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="d792b-584">`IMyDependency`i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d792b-585">`IMyDependency`jest zarejestrowany w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d792b-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d792b-586">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="d792b-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d792b-587">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="d792b-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="d792b-588">W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d792b-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d792b-589">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="d792b-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d792b-590">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="d792b-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d792b-591">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="d792b-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d792b-592">Na przykład `services.AddMvc()` dodaje Razor strony usług i wymagane przez MVC.</span><span class="sxs-lookup"><span data-stu-id="d792b-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="d792b-593">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="d792b-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d792b-594">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d792b-595">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string` ,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="d792b-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="d792b-596">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="d792b-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d792b-597">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d792b-598">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="d792b-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="d792b-599">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="d792b-599">Services injected into Startup</span></span>

<span data-ttu-id="d792b-600">Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="d792b-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d792b-601">Usługi można wstrzyknąć do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d792b-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d792b-602">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d792b-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d792b-603">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="d792b-603">Framework-provided services</span></span>

<span data-ttu-id="d792b-604">`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d792b-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d792b-605">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d792b-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d792b-606">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="d792b-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d792b-607">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="d792b-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="d792b-608">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="d792b-608">Service Type</span></span> | <span data-ttu-id="d792b-609">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="d792b-609">Lifetime</span></span> |
| ---
<span data-ttu-id="d792b-610">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-611">'Blazor'</span></span>
- <span data-ttu-id="d792b-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-612">'Identity'</span></span>
- <span data-ttu-id="d792b-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-614">'Razor'</span></span>
- <span data-ttu-id="d792b-615">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-616">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-617">'Blazor'</span></span>
- <span data-ttu-id="d792b-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-618">'Identity'</span></span>
- <span data-ttu-id="d792b-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-620">'Razor'</span></span>
- <span data-ttu-id="d792b-621">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-622">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-623">'Blazor'</span></span>
- <span data-ttu-id="d792b-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-624">'Identity'</span></span>
- <span data-ttu-id="d792b-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-626">'Razor'</span></span>
- <span data-ttu-id="d792b-627">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-628">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-629">'Blazor'</span></span>
- <span data-ttu-id="d792b-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-630">'Identity'</span></span>
- <span data-ttu-id="d792b-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-632">'Razor'</span></span>
- <span data-ttu-id="d792b-633">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-633">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-634">------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-635">'Blazor'</span></span>
- <span data-ttu-id="d792b-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-636">'Identity'</span></span>
- <span data-ttu-id="d792b-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-638">'Razor'</span></span>
- <span data-ttu-id="d792b-639">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-640">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-641">'Blazor'</span></span>
- <span data-ttu-id="d792b-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-642">'Identity'</span></span>
- <span data-ttu-id="d792b-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-644">'Razor'</span></span>
- <span data-ttu-id="d792b-645">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-645">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Przejściowe | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Przejściowe | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Przejściowe | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Pojedyncze | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Przejściowe | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Pojedyncze | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Pojedyncze | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Pojedyncze |</span><span class="sxs-lookup"><span data-stu-id="d792b-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d792b-647">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="d792b-647">Register additional services with extension methods</span></span>

<span data-ttu-id="d792b-648">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać pojedynczej `Add{SERVICE_NAME}` metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="d792b-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d792b-649">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="d792b-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="d792b-650">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="d792b-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d792b-651">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="d792b-651">Service lifetimes</span></span>

<span data-ttu-id="d792b-652">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d792b-653">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="d792b-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d792b-654">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="d792b-654">Transient</span></span>

<span data-ttu-id="d792b-655">Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d792b-656">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="d792b-657">Zakresie</span><span class="sxs-lookup"><span data-stu-id="d792b-657">Scoped</span></span>

<span data-ttu-id="d792b-658">Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="d792b-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="d792b-659">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` `InvokeAsync` metody lub.</span><span class="sxs-lookup"><span data-stu-id="d792b-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d792b-660">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="d792b-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d792b-661">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d792b-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d792b-662">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="d792b-662">Singleton</span></span>

<span data-ttu-id="d792b-663">Pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="d792b-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d792b-664">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="d792b-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d792b-665">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d792b-666">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="d792b-667">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="d792b-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d792b-668">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="d792b-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d792b-669">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="d792b-669">Service registration methods</span></span>

<span data-ttu-id="d792b-670">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="d792b-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d792b-671">Metoda</span><span class="sxs-lookup"><span data-stu-id="d792b-671">Method</span></span> | <span data-ttu-id="d792b-672">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="d792b-672">Automatic</span></span><br><span data-ttu-id="d792b-673">object</span><span class="sxs-lookup"><span data-stu-id="d792b-673">object</span></span><br><span data-ttu-id="d792b-674">myśl</span><span class="sxs-lookup"><span data-stu-id="d792b-674">disposal</span></span> | <span data-ttu-id="d792b-675">Wiele</span><span class="sxs-lookup"><span data-stu-id="d792b-675">Multiple</span></span><br><span data-ttu-id="d792b-676">implementacje</span><span class="sxs-lookup"><span data-stu-id="d792b-676">implementations</span></span> | <span data-ttu-id="d792b-677">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="d792b-677">Pass args</span></span> |
| ---
<span data-ttu-id="d792b-678">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-679">'Blazor'</span></span>
- <span data-ttu-id="d792b-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-680">'Identity'</span></span>
- <span data-ttu-id="d792b-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-682">'Razor'</span></span>
- <span data-ttu-id="d792b-683">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-683">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-684">--- | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-685">'Blazor'</span></span>
- <span data-ttu-id="d792b-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-686">'Identity'</span></span>
- <span data-ttu-id="d792b-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-688">'Razor'</span></span>
- <span data-ttu-id="d792b-689">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-690">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-691">'Blazor'</span></span>
- <span data-ttu-id="d792b-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-692">'Identity'</span></span>
- <span data-ttu-id="d792b-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-694">'Razor'</span></span>
- <span data-ttu-id="d792b-695">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-696">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-697">'Blazor'</span></span>
- <span data-ttu-id="d792b-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-698">'Identity'</span></span>
- <span data-ttu-id="d792b-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-700">'Razor'</span></span>
- <span data-ttu-id="d792b-701">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-702">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-703">'Blazor'</span></span>
- <span data-ttu-id="d792b-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-704">'Identity'</span></span>
- <span data-ttu-id="d792b-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-706">'Razor'</span></span>
- <span data-ttu-id="d792b-707">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-708">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-709">'Blazor'</span></span>
- <span data-ttu-id="d792b-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-710">'Identity'</span></span>
- <span data-ttu-id="d792b-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-712">'Razor'</span></span>
- <span data-ttu-id="d792b-713">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-714">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-715">'Blazor'</span></span>
- <span data-ttu-id="d792b-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-716">'Identity'</span></span>
- <span data-ttu-id="d792b-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-718">'Razor'</span></span>
- <span data-ttu-id="d792b-719">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-720">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-721">'Blazor'</span></span>
- <span data-ttu-id="d792b-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-722">'Identity'</span></span>
- <span data-ttu-id="d792b-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-724">'Razor'</span></span>
- <span data-ttu-id="d792b-725">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-726">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-727">'Blazor'</span></span>
- <span data-ttu-id="d792b-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-728">'Identity'</span></span>
- <span data-ttu-id="d792b-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-730">'Razor'</span></span>
- <span data-ttu-id="d792b-731">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-732">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-733">'Blazor'</span></span>
- <span data-ttu-id="d792b-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-734">'Identity'</span></span>
- <span data-ttu-id="d792b-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-736">'Razor'</span></span>
- <span data-ttu-id="d792b-737">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-738">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-739">'Blazor'</span></span>
- <span data-ttu-id="d792b-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-740">'Identity'</span></span>
- <span data-ttu-id="d792b-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-742">'Razor'</span></span>
- <span data-ttu-id="d792b-743">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-744">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-745">'Blazor'</span></span>
- <span data-ttu-id="d792b-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-746">'Identity'</span></span>
- <span data-ttu-id="d792b-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-748">'Razor'</span></span>
- <span data-ttu-id="d792b-749">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-750">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-751">'Blazor'</span></span>
- <span data-ttu-id="d792b-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-752">'Identity'</span></span>
- <span data-ttu-id="d792b-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-754">'Razor'</span></span>
- <span data-ttu-id="d792b-755">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-755">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-756">---------------: | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-757">'Blazor'</span></span>
- <span data-ttu-id="d792b-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-758">'Identity'</span></span>
- <span data-ttu-id="d792b-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-760">'Razor'</span></span>
- <span data-ttu-id="d792b-761">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-762">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-763">'Blazor'</span></span>
- <span data-ttu-id="d792b-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-764">'Identity'</span></span>
- <span data-ttu-id="d792b-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-766">'Razor'</span></span>
- <span data-ttu-id="d792b-767">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-768">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-769">'Blazor'</span></span>
- <span data-ttu-id="d792b-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-770">'Identity'</span></span>
- <span data-ttu-id="d792b-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-772">'Razor'</span></span>
- <span data-ttu-id="d792b-773">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-774">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-775">'Blazor'</span></span>
- <span data-ttu-id="d792b-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-776">'Identity'</span></span>
- <span data-ttu-id="d792b-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-778">'Razor'</span></span>
- <span data-ttu-id="d792b-779">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-780">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-781">'Blazor'</span></span>
- <span data-ttu-id="d792b-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-782">'Identity'</span></span>
- <span data-ttu-id="d792b-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-784">'Razor'</span></span>
- <span data-ttu-id="d792b-785">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-786">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-787">'Blazor'</span></span>
- <span data-ttu-id="d792b-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-788">'Identity'</span></span>
- <span data-ttu-id="d792b-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-790">'Razor'</span></span>
- <span data-ttu-id="d792b-791">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-792">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-793">'Blazor'</span></span>
- <span data-ttu-id="d792b-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-794">'Identity'</span></span>
- <span data-ttu-id="d792b-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-796">'Razor'</span></span>
- <span data-ttu-id="d792b-797">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-798">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-799">'Blazor'</span></span>
- <span data-ttu-id="d792b-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-800">'Identity'</span></span>
- <span data-ttu-id="d792b-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-802">'Razor'</span></span>
- <span data-ttu-id="d792b-803">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-804">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-805">'Blazor'</span></span>
- <span data-ttu-id="d792b-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-806">'Identity'</span></span>
- <span data-ttu-id="d792b-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-808">'Razor'</span></span>
- <span data-ttu-id="d792b-809">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d792b-810">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-811">'Blazor'</span></span>
- <span data-ttu-id="d792b-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-812">'Identity'</span></span>
- <span data-ttu-id="d792b-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-814">'Razor'</span></span>
- <span data-ttu-id="d792b-815">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-815">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-816">-------------: | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d792b-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d792b-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d792b-817">'Blazor'</span></span>
- <span data-ttu-id="d792b-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d792b-818">'Identity'</span></span>
- <span data-ttu-id="d792b-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d792b-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="d792b-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d792b-820">'Razor'</span></span>
- <span data-ttu-id="d792b-821">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d792b-821">'SignalR' uid:</span></span> 

<span data-ttu-id="d792b-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="d792b-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="d792b-823">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d792b-823">Example:</span></span><br><span data-ttu-id="d792b-824">`services.AddSingleton<IMyDep, MyDep>();`| Tak | Tak | Nie | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="d792b-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="d792b-825">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="d792b-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="d792b-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Tak | Tak | Tak | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="d792b-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="d792b-827">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d792b-827">Example:</span></span><br><span data-ttu-id="d792b-828">`services.AddSingleton<MyDep>();`| Tak | Nie | Nie | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="d792b-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="d792b-829">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="d792b-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="d792b-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Nie | Tak | Tak | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="d792b-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="d792b-831">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="d792b-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="d792b-832">`services.AddSingleton(new MyDep("A string!"));`| Nie | Nie | Tak |</span><span class="sxs-lookup"><span data-stu-id="d792b-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="d792b-833">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="d792b-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d792b-834">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d792b-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d792b-835">`TryAdd{LIFETIME}`Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d792b-836">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="d792b-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="d792b-837">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="d792b-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="d792b-838">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="d792b-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d792b-839">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="d792b-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d792b-840">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="d792b-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d792b-841">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="d792b-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d792b-842">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="d792b-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d792b-843">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="d792b-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d792b-844">Drugi wiersz rejestruje `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="d792b-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d792b-845">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="d792b-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d792b-846">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="d792b-846">Constructor injection behavior</span></span>

<span data-ttu-id="d792b-847">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="d792b-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d792b-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d792b-849">`ActivatorUtilities`jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="d792b-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d792b-850">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="d792b-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d792b-851">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="d792b-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="d792b-852">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="d792b-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d792b-853">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d792b-854">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d792b-854">Entity Framework contexts</span></span>

<span data-ttu-id="d792b-855">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="d792b-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d792b-856">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="d792b-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d792b-857">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="d792b-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d792b-858">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="d792b-858">Lifetime and registration options</span></span>

<span data-ttu-id="d792b-859">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z unikatowym identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d792b-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d792b-860">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="d792b-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="d792b-861">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="d792b-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d792b-862">`Operation`Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="d792b-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="d792b-863">`OperationService`Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="d792b-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d792b-864">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="d792b-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d792b-865">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` Usługa różni `IOperationTransient` się od `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="d792b-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d792b-866">`OperationService`odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="d792b-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d792b-867">Nowe wystąpienie daje inną wartość `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="d792b-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d792b-868">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` Usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="d792b-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d792b-869">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="d792b-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d792b-870">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="d792b-871">W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="d792b-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="d792b-872">`IOperationSingletonInstance`Usługa używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="d792b-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d792b-873">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="d792b-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d792b-874">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="d792b-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d792b-875">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="d792b-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d792b-876">Następnie na stronie zostaną wyświetlone wszystkie wartości klasy modelu strony i usługi `OperationId` za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="d792b-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="d792b-877">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="d792b-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d792b-878">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="d792b-878">**First request:**</span></span>

<span data-ttu-id="d792b-879">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="d792b-879">Controller operations:</span></span>

<span data-ttu-id="d792b-880">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d792b-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d792b-881">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d792b-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d792b-882">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-883">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-884">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="d792b-884">`OperationService` operations:</span></span>

<span data-ttu-id="d792b-885">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d792b-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d792b-886">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d792b-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d792b-887">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-888">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-889">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="d792b-889">**Second request:**</span></span>

<span data-ttu-id="d792b-890">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="d792b-890">Controller operations:</span></span>

<span data-ttu-id="d792b-891">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d792b-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d792b-892">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d792b-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d792b-893">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-894">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-895">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="d792b-895">`OperationService` operations:</span></span>

<span data-ttu-id="d792b-896">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d792b-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d792b-897">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d792b-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d792b-898">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d792b-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d792b-899">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d792b-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d792b-900">Zauważ, że `OperationId` wartości różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="d792b-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d792b-901">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="d792b-901">*Transient* objects are always different.</span></span> <span data-ttu-id="d792b-902">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="d792b-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d792b-903">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="d792b-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d792b-904">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="d792b-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d792b-905">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, czy `Operation` wystąpienie jest dostarczone w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d792b-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d792b-906">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="d792b-906">Call services from main</span></span>

<span data-ttu-id="d792b-907">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d792b-908">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="d792b-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d792b-909">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d792b-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="d792b-910">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="d792b-910">Scope validation</span></span>

<span data-ttu-id="d792b-911">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="d792b-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d792b-912">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="d792b-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d792b-913">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="d792b-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d792b-914">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="d792b-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d792b-915">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d792b-916">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="d792b-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d792b-917">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="d792b-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d792b-918">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="d792b-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d792b-919">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d792b-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="d792b-920">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="d792b-920">Request Services</span></span>

<span data-ttu-id="d792b-921">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="d792b-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d792b-922">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d792b-923">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="d792b-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d792b-924">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="d792b-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d792b-925">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="d792b-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="d792b-926">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="d792b-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d792b-927">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="d792b-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d792b-928">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="d792b-928">Design services for dependency injection</span></span>

<span data-ttu-id="d792b-929">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="d792b-929">Best practices are to:</span></span>

* <span data-ttu-id="d792b-930">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="d792b-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d792b-931">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="d792b-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="d792b-932">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="d792b-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="d792b-933">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d792b-934">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d792b-935">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="d792b-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d792b-936">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d792b-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d792b-937">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="d792b-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d792b-938">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d792b-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d792b-939">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="d792b-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d792b-940">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="d792b-940">Disposal of services</span></span>

<span data-ttu-id="d792b-941">Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="d792b-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d792b-942">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="d792b-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="d792b-943">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="d792b-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="d792b-944">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d792b-944">In the following example:</span></span>

* <span data-ttu-id="d792b-945">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="d792b-946">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="d792b-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="d792b-947">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="d792b-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="d792b-948">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d792b-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="d792b-949">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="d792b-949">Default service container replacement</span></span>

<span data-ttu-id="d792b-950">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="d792b-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d792b-951">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="d792b-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d792b-952">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="d792b-952">Property injection</span></span>
* <span data-ttu-id="d792b-953">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="d792b-953">Injection based on name</span></span>
* <span data-ttu-id="d792b-954">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="d792b-954">Child containers</span></span>
* <span data-ttu-id="d792b-955">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="d792b-955">Custom lifetime management</span></span>
* <span data-ttu-id="d792b-956">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="d792b-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="d792b-957">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="d792b-957">Convention-based registration</span></span>

<span data-ttu-id="d792b-958">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="d792b-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d792b-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="d792b-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d792b-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d792b-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d792b-961">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="d792b-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d792b-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="d792b-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d792b-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="d792b-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d792b-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d792b-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d792b-965">Unity</span><span class="sxs-lookup"><span data-stu-id="d792b-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d792b-966">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="d792b-966">Thread safety</span></span>

<span data-ttu-id="d792b-967">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="d792b-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="d792b-968">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="d792b-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d792b-969">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="d792b-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d792b-970">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="d792b-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d792b-971">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="d792b-971">Recommendations</span></span>

* <span data-ttu-id="d792b-972">`async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="d792b-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d792b-973">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d792b-974">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d792b-975">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="d792b-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d792b-976">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d792b-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d792b-977">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="d792b-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d792b-978">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="d792b-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="d792b-979">Należy unikać statycznego dostępu do usług (na przykład statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="d792b-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="d792b-980">Unikaj używania *wzorca lokalizatora usługi*, który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="d792b-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="d792b-981">Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="d792b-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="d792b-982">**Prawidłowy**</span><span class="sxs-lookup"><span data-stu-id="d792b-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="d792b-983">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="d792b-983">**Correct**:</span></span>

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

  * <span data-ttu-id="d792b-984">Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie wykonywania za pomocą polecenia <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="d792b-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="d792b-985">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d792b-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d792b-986">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="d792b-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d792b-987">Wyjątki są rzadko &mdash; w większości specjalnych przypadków w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="d792b-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d792b-988">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="d792b-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d792b-989">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="d792b-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d792b-990">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d792b-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d792b-991">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d792b-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="d792b-992">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="d792b-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d792b-993">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="d792b-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d792b-994">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="d792b-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
