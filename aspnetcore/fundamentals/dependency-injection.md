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
ms.openlocfilehash: 8a4ee8bee09b3d6e9de932dab17bbc5c6494a492
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767528"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="cd4f6-103">Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="cd4f6-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="cd4f6-104">[Steve Kowalski](https://ardalis.com/) i [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="cd4f6-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd4f6-105">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="cd4f6-106">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="cd4f6-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd4f6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="cd4f6-108">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="cd4f6-108">Overview of dependency injection</span></span>

<span data-ttu-id="cd4f6-109">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="cd4f6-110">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="cd4f6-111">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="cd4f6-112">`MyDependency` Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="cd4f6-113">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="cd4f6-114">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="cd4f6-115">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="cd4f6-116">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="cd4f6-117">W dużym projekcie z wieloma klasami `MyDependency`, w zależności od tego, kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="cd4f6-118">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="cd4f6-119">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="cd4f6-120">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="cd4f6-121">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="cd4f6-122">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="cd4f6-123">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="cd4f6-124">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="cd4f6-125">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="cd4f6-126">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="cd4f6-127">W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="cd4f6-128">Ten interfejs jest implementowany przez konkretny typ `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="cd4f6-129">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="cd4f6-130">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="cd4f6-131">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="cd4f6-132">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="cd4f6-133">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="cd4f6-134">`IMyDependency`i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="cd4f6-135">`IMyDependency`jest zarejestrowany w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd4f6-136">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="cd4f6-137">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="cd4f6-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="cd4f6-138">W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana w konkretnym typie `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="cd4f6-139">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="cd4f6-140">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="cd4f6-141">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="cd4f6-142">Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="cd4f6-143">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="cd4f6-144">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="cd4f6-145">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string`,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="cd4f6-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="cd4f6-146">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="cd4f6-147">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="cd4f6-148">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="cd4f6-149">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="cd4f6-149">Services injected into Startup</span></span>

<span data-ttu-id="cd4f6-150">Tylko następujące typy usług można wstrzyknąć do konstruktora, `Startup` gdy jest używany host generyczny (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="cd4f6-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="cd4f6-151">Usługi można wstrzyknąć do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="cd4f6-152">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="cd4f6-153">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="cd4f6-153">Framework-provided services</span></span>

<span data-ttu-id="cd4f6-154">`Startup.ConfigureServices` Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="cd4f6-155">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="cd4f6-156">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="cd4f6-157">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="cd4f6-158">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="cd4f6-158">Service Type</span></span> | <span data-ttu-id="cd4f6-159">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="cd4f6-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="cd4f6-160">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="cd4f6-161">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="cd4f6-162">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="cd4f6-163">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="cd4f6-164">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="cd4f6-165">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="cd4f6-166">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="cd4f6-167">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="cd4f6-168">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="cd4f6-169">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="cd4f6-170">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="cd4f6-171">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="cd4f6-172">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="cd4f6-173">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="cd4f6-174">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="cd4f6-174">Register additional services with extension methods</span></span>

<span data-ttu-id="cd4f6-175">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać `Add{SERVICE_NAME}` pojedynczej metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="cd4f6-176">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="cd4f6-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="cd4f6-177">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="cd4f6-178">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="cd4f6-178">Service lifetimes</span></span>

<span data-ttu-id="cd4f6-179">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="cd4f6-180">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="cd4f6-181">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-181">Transient</span></span>

<span data-ttu-id="cd4f6-182">Przejściowe usługi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>okresu istnienia są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="cd4f6-183">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="cd4f6-184">Zakresie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-184">Scoped</span></span>

<span data-ttu-id="cd4f6-185">Usługi okresu istnienia w<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>zakresie () są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="cd4f6-186">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` metody `InvokeAsync` lub.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="cd4f6-187">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="cd4f6-188">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="cd4f6-189">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-189">Singleton</span></span>

<span data-ttu-id="cd4f6-190">Pojedyncze usługi okresu istnienia (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="cd4f6-191">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="cd4f6-192">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="cd4f6-193">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="cd4f6-194">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="cd4f6-195">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="cd4f6-196">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="cd4f6-196">Service registration methods</span></span>

<span data-ttu-id="cd4f6-197">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="cd4f6-198">Metoda</span><span class="sxs-lookup"><span data-stu-id="cd4f6-198">Method</span></span> | <span data-ttu-id="cd4f6-199">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="cd4f6-199">Automatic</span></span><br><span data-ttu-id="cd4f6-200">obiekt</span><span class="sxs-lookup"><span data-stu-id="cd4f6-200">object</span></span><br><span data-ttu-id="cd4f6-201">myśl</span><span class="sxs-lookup"><span data-stu-id="cd4f6-201">disposal</span></span> | <span data-ttu-id="cd4f6-202">Wiele</span><span class="sxs-lookup"><span data-stu-id="cd4f6-202">Multiple</span></span><br><span data-ttu-id="cd4f6-203">implementacje</span><span class="sxs-lookup"><span data-stu-id="cd4f6-203">implementations</span></span> | <span data-ttu-id="cd4f6-204">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="cd4f6-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="cd4f6-205">Przykład:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="cd4f6-206">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-206">Yes</span></span> | <span data-ttu-id="cd4f6-207">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-207">Yes</span></span> | <span data-ttu-id="cd4f6-208">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="cd4f6-209">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="cd4f6-210">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-210">Yes</span></span> | <span data-ttu-id="cd4f6-211">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-211">Yes</span></span> | <span data-ttu-id="cd4f6-212">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="cd4f6-213">Przykład:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="cd4f6-214">Yes</span><span class="sxs-lookup"><span data-stu-id="cd4f6-214">Yes</span></span> | <span data-ttu-id="cd4f6-215">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-215">No</span></span> | <span data-ttu-id="cd4f6-216">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="cd4f6-217">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="cd4f6-218">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-218">No</span></span> | <span data-ttu-id="cd4f6-219">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-219">Yes</span></span> | <span data-ttu-id="cd4f6-220">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="cd4f6-221">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="cd4f6-222">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-222">No</span></span> | <span data-ttu-id="cd4f6-223">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-223">No</span></span> | <span data-ttu-id="cd4f6-224">Yes</span><span class="sxs-lookup"><span data-stu-id="cd4f6-224">Yes</span></span> |

<span data-ttu-id="cd4f6-225">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="cd4f6-226">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="cd4f6-227">`TryAdd{LIFETIME}`Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="cd4f6-228">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="cd4f6-229">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="cd4f6-230">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="cd4f6-231">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="cd4f6-232">Wiele usług jest rozpoznawanych `IEnumerable<{SERVICE}>`za pośrednictwem.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="cd4f6-233">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="cd4f6-234">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="cd4f6-235">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="cd4f6-236">Drugi wiersz rejestruje `MyDep` `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="cd4f6-237">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja: `MyDep`</span><span class="sxs-lookup"><span data-stu-id="cd4f6-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="cd4f6-238">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="cd4f6-238">Constructor injection behavior</span></span>

<span data-ttu-id="cd4f6-239">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="cd4f6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="cd4f6-241">`ActivatorUtilities`jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="cd4f6-242">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="cd4f6-243">Gdy usługi są rozwiązane `IServiceProvider` przez `ActivatorUtilities`lub, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="cd4f6-244">Gdy usługi są rozwiązane `ActivatorUtilities`przez, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="cd4f6-245">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="cd4f6-246">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cd4f6-246">Entity Framework contexts</span></span>

<span data-ttu-id="cd4f6-247">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="cd4f6-248">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez Przeciążenie [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) Overload podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="cd4f6-249">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="cd4f6-250">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="cd4f6-250">Lifetime and registration options</span></span>

<span data-ttu-id="cd4f6-251">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje `OperationId`z unikatowym identyfikatorem.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="cd4f6-252">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="cd4f6-253">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="cd4f6-254">`Operation` Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="cd4f6-255">`OperationService` Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="cd4f6-256">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="cd4f6-257">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` `IOperationTransient` usługa różni się od `OperationId` `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="cd4f6-258">`OperationService`odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="cd4f6-259">Nowe wystąpienie daje inną `OperationId`wartość.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="cd4f6-260">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="cd4f6-261">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="cd4f6-262">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="cd4f6-263">W `Startup.ConfigureServices`programie każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="cd4f6-264">`IOperationSingletonInstance` Usługa używa określonego wystąpienia o ZNANYm identyfikatorze `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="cd4f6-265">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="cd4f6-266">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="cd4f6-267">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="cd4f6-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="cd4f6-268">Następnie na stronie zostaną wyświetlone wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="cd4f6-269">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="cd4f6-270">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="cd4f6-270">**First request:**</span></span>

<span data-ttu-id="cd4f6-271">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-271">Controller operations:</span></span>

<span data-ttu-id="cd4f6-272">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="cd4f6-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="cd4f6-273">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cd4f6-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cd4f6-274">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-275">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-276">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="cd4f6-276">`OperationService` operations:</span></span>

<span data-ttu-id="cd4f6-277">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="cd4f6-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="cd4f6-278">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cd4f6-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cd4f6-279">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-280">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-281">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="cd4f6-281">**Second request:**</span></span>

<span data-ttu-id="cd4f6-282">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-282">Controller operations:</span></span>

<span data-ttu-id="cd4f6-283">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="cd4f6-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="cd4f6-284">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cd4f6-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cd4f6-285">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-286">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-287">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="cd4f6-287">`OperationService` operations:</span></span>

<span data-ttu-id="cd4f6-288">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="cd4f6-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="cd4f6-289">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cd4f6-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cd4f6-290">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-291">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-292">Zauważ, że wartości `OperationId` różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="cd4f6-293">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-293">*Transient* objects are always different.</span></span> <span data-ttu-id="cd4f6-294">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="cd4f6-295">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="cd4f6-296">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="cd4f6-297">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, `Operation` czy wystąpienie jest dostarczone `Startup.ConfigureServices`w.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="cd4f6-298">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-298">Call services from main</span></span>

<span data-ttu-id="cd4f6-299">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="cd4f6-300">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="cd4f6-301">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="cd4f6-302">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="cd4f6-302">Scope validation</span></span>

<span data-ttu-id="cd4f6-303">Gdy aplikacja jest uruchomiona w środowisku deweloperskim i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do skompilowania hosta, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="cd4f6-304">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="cd4f6-305">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="cd4f6-306">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="cd4f6-307">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="cd4f6-308">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="cd4f6-309">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="cd4f6-310">Sprawdzanie poprawności zakresów usług przechwytuje te `BuildServiceProvider` sytuacje, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="cd4f6-311">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="cd4f6-312">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="cd4f6-312">Request Services</span></span>

<span data-ttu-id="cd4f6-313">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="cd4f6-314">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="cd4f6-315">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices`, nie. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="cd4f6-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="cd4f6-316">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="cd4f6-317">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól platformie na wstrzyknięcie zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="cd4f6-318">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="cd4f6-319">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp `RequestServices` do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="cd4f6-320">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="cd4f6-320">Design services for dependency injection</span></span>

<span data-ttu-id="cd4f6-321">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-321">Best practices are to:</span></span>

* <span data-ttu-id="cd4f6-322">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="cd4f6-323">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="cd4f6-324">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="cd4f6-325">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="cd4f6-326">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="cd4f6-327">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="cd4f6-328">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="cd4f6-329">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="cd4f6-330">Należy pamiętać, że klasy Razor Pages modelu strony i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="cd4f6-331">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="cd4f6-332">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="cd4f6-332">Disposal of services</span></span>

<span data-ttu-id="cd4f6-333">Kontener wywołuje <xref:System.IDisposable.Dispose*> typy, które <xref:System.IDisposable> tworzy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="cd4f6-334">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="cd4f6-335">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="cd4f6-336">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-336">In the following example:</span></span>

* <span data-ttu-id="cd4f6-337">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="cd4f6-338">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="cd4f6-339">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="cd4f6-340">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="cd4f6-341">Aby zapoznać się z omówieniem opcji usuwania usług, zobacz [cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="cd4f6-342">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="cd4f6-342">Default service container replacement</span></span>

<span data-ttu-id="cd4f6-343">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="cd4f6-344">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="cd4f6-345">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="cd4f6-345">Property injection</span></span>
* <span data-ttu-id="cd4f6-346">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-346">Injection based on name</span></span>
* <span data-ttu-id="cd4f6-347">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="cd4f6-347">Child containers</span></span>
* <span data-ttu-id="cd4f6-348">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="cd4f6-348">Custom lifetime management</span></span>
* <span data-ttu-id="cd4f6-349">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="cd4f6-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="cd4f6-350">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="cd4f6-350">Convention-based registration</span></span>

<span data-ttu-id="cd4f6-351">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="cd4f6-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="cd4f6-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="cd4f6-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="cd4f6-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="cd4f6-354">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="cd4f6-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="cd4f6-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="cd4f6-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="cd4f6-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="cd4f6-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="cd4f6-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="cd4f6-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="cd4f6-358">Unity</span><span class="sxs-lookup"><span data-stu-id="cd4f6-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="cd4f6-359">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="cd4f6-359">Thread safety</span></span>

<span data-ttu-id="cd4f6-360">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="cd4f6-361">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="cd4f6-362">Metoda fabryki pojedynczej usługi, taka jak drugi argument dla [\<addsingleton TService> (IServiceCollection, Func\<IServiceProvider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="cd4f6-363">Podobnie jak w przypadku`static`konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="cd4f6-364">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="cd4f6-364">Recommendations</span></span>

* <span data-ttu-id="cd4f6-365">`async/await`Rozpoznawanie `Task` usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="cd4f6-366">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="cd4f6-367">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="cd4f6-368">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="cd4f6-369">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="cd4f6-370">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="cd4f6-371">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="cd4f6-372">Należy unikać statycznego dostępu do usług (na przykład statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="cd4f6-373">Unikaj używania *wzorca lokalizatora usługi*.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="cd4f6-374">Na przykład nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="cd4f6-375">**Prawidłowy**</span><span class="sxs-lookup"><span data-stu-id="cd4f6-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="cd4f6-376">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-376">**Correct**:</span></span>

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

* <span data-ttu-id="cd4f6-377">Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="cd4f6-378">Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="cd4f6-379">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="cd4f6-380">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="cd4f6-381">Wyjątki są rzadko&mdash;w większości specjalnych przypadków w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="cd4f6-382">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="cd4f6-383">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd4f6-384">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="cd4f6-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="cd4f6-385">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="cd4f6-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="cd4f6-386">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="cd4f6-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="cd4f6-387">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="cd4f6-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="cd4f6-388">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="cd4f6-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd4f6-389">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="cd4f6-390">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="cd4f6-391">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd4f6-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="cd4f6-392">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="cd4f6-392">Overview of dependency injection</span></span>

<span data-ttu-id="cd4f6-393">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="cd4f6-394">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="cd4f6-395">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="cd4f6-396">`MyDependency` Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="cd4f6-397">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="cd4f6-398">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="cd4f6-399">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="cd4f6-400">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="cd4f6-401">W dużym projekcie z wieloma klasami `MyDependency`, w zależności od tego, kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="cd4f6-402">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="cd4f6-403">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="cd4f6-404">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="cd4f6-405">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="cd4f6-406">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="cd4f6-407">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="cd4f6-408">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="cd4f6-409">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="cd4f6-410">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="cd4f6-411">W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="cd4f6-412">Ten interfejs jest implementowany przez konkretny typ `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="cd4f6-413">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="cd4f6-414">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="cd4f6-415">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="cd4f6-416">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="cd4f6-417">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="cd4f6-418">`IMyDependency`i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="cd4f6-419">`IMyDependency`jest zarejestrowany w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd4f6-420">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="cd4f6-421">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="cd4f6-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="cd4f6-422">W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana w konkretnym typie `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="cd4f6-423">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="cd4f6-424">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="cd4f6-425">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="cd4f6-426">Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="cd4f6-427">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="cd4f6-428">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="cd4f6-429">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string`,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="cd4f6-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="cd4f6-430">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="cd4f6-431">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="cd4f6-432">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="cd4f6-433">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="cd4f6-433">Services injected into Startup</span></span>

<span data-ttu-id="cd4f6-434">Tylko następujące typy usług można wstrzyknąć do konstruktora, `Startup` gdy jest używany host generyczny (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="cd4f6-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="cd4f6-435">Usługi można wstrzyknąć do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="cd4f6-436">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="cd4f6-437">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="cd4f6-437">Framework-provided services</span></span>

<span data-ttu-id="cd4f6-438">`Startup.ConfigureServices` Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="cd4f6-439">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="cd4f6-440">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="cd4f6-441">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="cd4f6-442">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="cd4f6-442">Service Type</span></span> | <span data-ttu-id="cd4f6-443">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="cd4f6-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="cd4f6-444">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="cd4f6-445">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="cd4f6-446">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="cd4f6-447">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="cd4f6-448">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="cd4f6-449">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="cd4f6-450">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="cd4f6-451">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="cd4f6-452">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="cd4f6-453">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="cd4f6-454">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="cd4f6-455">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="cd4f6-456">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="cd4f6-457">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="cd4f6-458">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="cd4f6-458">Register additional services with extension methods</span></span>

<span data-ttu-id="cd4f6-459">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać `Add{SERVICE_NAME}` pojedynczej metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="cd4f6-460">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="cd4f6-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="cd4f6-461">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="cd4f6-462">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="cd4f6-462">Service lifetimes</span></span>

<span data-ttu-id="cd4f6-463">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="cd4f6-464">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="cd4f6-465">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="cd4f6-465">Transient</span></span>

<span data-ttu-id="cd4f6-466">Przejściowe usługi<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>okresu istnienia są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="cd4f6-467">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="cd4f6-468">Zakresie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-468">Scoped</span></span>

<span data-ttu-id="cd4f6-469">Usługi okresu istnienia w<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>zakresie () są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="cd4f6-470">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` metody `InvokeAsync` lub.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="cd4f6-471">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-471">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="cd4f6-472">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="cd4f6-473">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-473">Singleton</span></span>

<span data-ttu-id="cd4f6-474">Pojedyncze usługi okresu istnienia (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="cd4f6-475">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="cd4f6-476">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="cd4f6-477">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="cd4f6-478">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="cd4f6-479">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="cd4f6-480">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="cd4f6-480">Service registration methods</span></span>

<span data-ttu-id="cd4f6-481">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="cd4f6-482">Metoda</span><span class="sxs-lookup"><span data-stu-id="cd4f6-482">Method</span></span> | <span data-ttu-id="cd4f6-483">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="cd4f6-483">Automatic</span></span><br><span data-ttu-id="cd4f6-484">obiekt</span><span class="sxs-lookup"><span data-stu-id="cd4f6-484">object</span></span><br><span data-ttu-id="cd4f6-485">myśl</span><span class="sxs-lookup"><span data-stu-id="cd4f6-485">disposal</span></span> | <span data-ttu-id="cd4f6-486">Wiele</span><span class="sxs-lookup"><span data-stu-id="cd4f6-486">Multiple</span></span><br><span data-ttu-id="cd4f6-487">implementacje</span><span class="sxs-lookup"><span data-stu-id="cd4f6-487">implementations</span></span> | <span data-ttu-id="cd4f6-488">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="cd4f6-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="cd4f6-489">Przykład:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="cd4f6-490">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-490">Yes</span></span> | <span data-ttu-id="cd4f6-491">Yes</span><span class="sxs-lookup"><span data-stu-id="cd4f6-491">Yes</span></span> | <span data-ttu-id="cd4f6-492">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="cd4f6-493">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="cd4f6-494">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-494">Yes</span></span> | <span data-ttu-id="cd4f6-495">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-495">Yes</span></span> | <span data-ttu-id="cd4f6-496">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="cd4f6-497">Przykład:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="cd4f6-498">Yes</span><span class="sxs-lookup"><span data-stu-id="cd4f6-498">Yes</span></span> | <span data-ttu-id="cd4f6-499">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-499">No</span></span> | <span data-ttu-id="cd4f6-500">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="cd4f6-501">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="cd4f6-502">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-502">No</span></span> | <span data-ttu-id="cd4f6-503">Yes</span><span class="sxs-lookup"><span data-stu-id="cd4f6-503">Yes</span></span> | <span data-ttu-id="cd4f6-504">Tak</span><span class="sxs-lookup"><span data-stu-id="cd4f6-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="cd4f6-505">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="cd4f6-506">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-506">No</span></span> | <span data-ttu-id="cd4f6-507">Nie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-507">No</span></span> | <span data-ttu-id="cd4f6-508">Yes</span><span class="sxs-lookup"><span data-stu-id="cd4f6-508">Yes</span></span> |

<span data-ttu-id="cd4f6-509">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="cd4f6-510">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="cd4f6-511">`TryAdd{LIFETIME}`Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="cd4f6-512">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="cd4f6-513">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="cd4f6-514">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="cd4f6-515">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="cd4f6-516">Wiele usług jest rozpoznawanych `IEnumerable<{SERVICE}>`za pośrednictwem.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="cd4f6-517">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="cd4f6-518">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="cd4f6-519">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="cd4f6-520">Drugi wiersz rejestruje `MyDep` `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="cd4f6-521">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja: `MyDep`</span><span class="sxs-lookup"><span data-stu-id="cd4f6-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="cd4f6-522">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="cd4f6-522">Constructor injection behavior</span></span>

<span data-ttu-id="cd4f6-523">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="cd4f6-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="cd4f6-525">`ActivatorUtilities`jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="cd4f6-526">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="cd4f6-527">Gdy usługi są rozwiązane `IServiceProvider` przez `ActivatorUtilities`lub, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="cd4f6-528">Gdy usługi są rozwiązane `ActivatorUtilities`przez, [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-528">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="cd4f6-529">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="cd4f6-530">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cd4f6-530">Entity Framework contexts</span></span>

<span data-ttu-id="cd4f6-531">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="cd4f6-532">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez Przeciążenie [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) Overload podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="cd4f6-533">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="cd4f6-534">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="cd4f6-534">Lifetime and registration options</span></span>

<span data-ttu-id="cd4f6-535">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje `OperationId`z unikatowym identyfikatorem.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="cd4f6-536">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="cd4f6-537">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="cd4f6-538">`Operation` Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="cd4f6-539">`OperationService` Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="cd4f6-540">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="cd4f6-541">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` `IOperationTransient` usługa różni się od `OperationId` `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="cd4f6-542">`OperationService`odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="cd4f6-543">Nowe wystąpienie daje inną `OperationId`wartość.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="cd4f6-544">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="cd4f6-545">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="cd4f6-546">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="cd4f6-547">W `Startup.ConfigureServices`programie każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="cd4f6-548">`IOperationSingletonInstance` Usługa używa określonego wystąpienia o ZNANYm identyfikatorze `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="cd4f6-549">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="cd4f6-550">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="cd4f6-551">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i. `OperationService`</span><span class="sxs-lookup"><span data-stu-id="cd4f6-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="cd4f6-552">Następnie na stronie zostaną wyświetlone wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="cd4f6-553">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="cd4f6-554">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="cd4f6-554">**First request:**</span></span>

<span data-ttu-id="cd4f6-555">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-555">Controller operations:</span></span>

<span data-ttu-id="cd4f6-556">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="cd4f6-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="cd4f6-557">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cd4f6-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cd4f6-558">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-559">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-560">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="cd4f6-560">`OperationService` operations:</span></span>

<span data-ttu-id="cd4f6-561">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="cd4f6-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="cd4f6-562">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cd4f6-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cd4f6-563">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-564">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-565">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="cd4f6-565">**Second request:**</span></span>

<span data-ttu-id="cd4f6-566">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-566">Controller operations:</span></span>

<span data-ttu-id="cd4f6-567">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="cd4f6-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="cd4f6-568">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cd4f6-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cd4f6-569">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-570">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-571">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="cd4f6-571">`OperationService` operations:</span></span>

<span data-ttu-id="cd4f6-572">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="cd4f6-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="cd4f6-573">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cd4f6-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cd4f6-574">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cd4f6-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cd4f6-575">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cd4f6-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cd4f6-576">Zauważ, że wartości `OperationId` różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="cd4f6-577">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-577">*Transient* objects are always different.</span></span> <span data-ttu-id="cd4f6-578">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="cd4f6-579">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="cd4f6-580">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="cd4f6-581">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, `Operation` czy wystąpienie jest dostarczone `Startup.ConfigureServices`w.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="cd4f6-582">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="cd4f6-582">Call services from main</span></span>

<span data-ttu-id="cd4f6-583">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="cd4f6-584">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="cd4f6-585">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="cd4f6-586">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="cd4f6-586">Scope validation</span></span>

<span data-ttu-id="cd4f6-587">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="cd4f6-588">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="cd4f6-589">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="cd4f6-590">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="cd4f6-591">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="cd4f6-592">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="cd4f6-593">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="cd4f6-594">Sprawdzanie poprawności zakresów usług przechwytuje te `BuildServiceProvider` sytuacje, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="cd4f6-595">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="cd4f6-596">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="cd4f6-596">Request Services</span></span>

<span data-ttu-id="cd4f6-597">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="cd4f6-598">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="cd4f6-599">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices`, nie. `ApplicationServices`</span><span class="sxs-lookup"><span data-stu-id="cd4f6-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="cd4f6-600">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="cd4f6-601">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="cd4f6-602">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="cd4f6-603">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp `RequestServices` do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="cd4f6-604">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="cd4f6-604">Design services for dependency injection</span></span>

<span data-ttu-id="cd4f6-605">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-605">Best practices are to:</span></span>

* <span data-ttu-id="cd4f6-606">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="cd4f6-607">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="cd4f6-608">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="cd4f6-609">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="cd4f6-610">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="cd4f6-611">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="cd4f6-612">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="cd4f6-613">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="cd4f6-614">Należy pamiętać, że Razor klasy klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="cd4f6-615">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="cd4f6-616">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="cd4f6-616">Disposal of services</span></span>

<span data-ttu-id="cd4f6-617">Kontener wywołuje <xref:System.IDisposable.Dispose*> typy, które <xref:System.IDisposable> tworzy.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="cd4f6-618">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="cd4f6-619">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="cd4f6-620">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-620">In the following example:</span></span>

* <span data-ttu-id="cd4f6-621">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="cd4f6-622">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="cd4f6-623">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="cd4f6-624">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="cd4f6-625">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="cd4f6-625">Default service container replacement</span></span>

<span data-ttu-id="cd4f6-626">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="cd4f6-627">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="cd4f6-628">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="cd4f6-628">Property injection</span></span>
* <span data-ttu-id="cd4f6-629">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="cd4f6-629">Injection based on name</span></span>
* <span data-ttu-id="cd4f6-630">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="cd4f6-630">Child containers</span></span>
* <span data-ttu-id="cd4f6-631">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="cd4f6-631">Custom lifetime management</span></span>
* <span data-ttu-id="cd4f6-632">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="cd4f6-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="cd4f6-633">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="cd4f6-633">Convention-based registration</span></span>

<span data-ttu-id="cd4f6-634">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="cd4f6-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="cd4f6-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="cd4f6-636">DryIoc</span><span class="sxs-lookup"><span data-stu-id="cd4f6-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="cd4f6-637">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="cd4f6-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="cd4f6-638">LightInject</span><span class="sxs-lookup"><span data-stu-id="cd4f6-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="cd4f6-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="cd4f6-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="cd4f6-640">Stashbox</span><span class="sxs-lookup"><span data-stu-id="cd4f6-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="cd4f6-641">Unity</span><span class="sxs-lookup"><span data-stu-id="cd4f6-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="cd4f6-642">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="cd4f6-642">Thread safety</span></span>

<span data-ttu-id="cd4f6-643">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="cd4f6-644">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="cd4f6-645">Metoda fabryki pojedynczej usługi, taka jak drugi argument dla [\<addsingleton TService> (IServiceCollection, Func\<IServiceProvider, TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="cd4f6-646">Podobnie jak w przypadku`static`konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="cd4f6-647">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="cd4f6-647">Recommendations</span></span>

* <span data-ttu-id="cd4f6-648">`async/await`Rozpoznawanie `Task` usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="cd4f6-649">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="cd4f6-650">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="cd4f6-651">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="cd4f6-652">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="cd4f6-653">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="cd4f6-654">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="cd4f6-655">Należy unikać statycznego dostępu do usług (na przykład statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="cd4f6-656">Unikaj używania *wzorca lokalizatora usługi*, który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="cd4f6-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="cd4f6-657">Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="cd4f6-658">**Prawidłowy**</span><span class="sxs-lookup"><span data-stu-id="cd4f6-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="cd4f6-659">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="cd4f6-659">**Correct**:</span></span>

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

  * <span data-ttu-id="cd4f6-660">Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie <xref:System.IServiceProvider.GetService*>wykonywania za pomocą polecenia.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="cd4f6-661">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="cd4f6-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="cd4f6-662">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="cd4f6-663">Wyjątki są rzadko&mdash;w większości specjalnych przypadków w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="cd4f6-664">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="cd4f6-665">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="cd4f6-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd4f6-666">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="cd4f6-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="cd4f6-667">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="cd4f6-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="cd4f6-668">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="cd4f6-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="cd4f6-669">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="cd4f6-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="cd4f6-670">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="cd4f6-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
