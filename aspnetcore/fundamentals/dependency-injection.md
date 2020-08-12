---
title: Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)
author: rick-anderson
description: Dowiedz się, w jaki sposób ASP.NET Core implementuje iniekcję zależności i jak z niej korzystać.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130668"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="be595-103">Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="be595-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be595-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="be595-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="be595-105">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="be595-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="be595-106">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="be595-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="be595-107">Aby uzyskać więcej informacji na temat wstrzykiwania zależności opcji, zobacz <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="be595-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="be595-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be595-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="be595-109">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="be595-109">Overview of dependency injection</span></span>

<span data-ttu-id="be595-110">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="be595-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="be595-111">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="be595-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="be595-112">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="be595-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="be595-113">`MyDependency`Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="be595-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="be595-114">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="be595-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="be595-115">Zależności kodu, takie jak w poprzednim przykładzie, są problematyczne i należy je uniknąć z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="be595-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="be595-116">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="be595-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="be595-117">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="be595-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="be595-118">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="be595-119">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="be595-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="be595-120">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="be595-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="be595-121">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="be595-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="be595-122">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="be595-123">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="be595-124">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="be595-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="be595-125">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="be595-126">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="be595-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="be595-127">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="be595-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="be595-128">W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="be595-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="be595-129">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="be595-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="be595-130">W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="be595-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="be595-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda rejestruje usługę z okresem istnienia w określonym zakresie, okresem istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="be595-132">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="be595-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="be595-133">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="be595-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="be595-134">Ze wzorem DI:</span><span class="sxs-lookup"><span data-stu-id="be595-134">With the DI pattern:</span></span>

* <span data-ttu-id="be595-135">Kontroler nie używa konkretnego typu `MyDependency` , tylko interfejsu `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="be595-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="be595-136">Dzięki temu można łatwo zmienić implementację używaną przez kontroler bez modyfikowania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="be595-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="be595-137">Kontroler nie tworzy wystąpienia `MyDependency` , jest tworzone przez di kontener.</span><span class="sxs-lookup"><span data-stu-id="be595-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="be595-138">Implementację `IMyDependency` interfejsu można ulepszyć za pomocą wbudowanego interfejsu API rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="be595-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="be595-139">Zaktualizowana `ConfigureServices` Metoda rejestruje nową `IMyDependency` implementację:</span><span class="sxs-lookup"><span data-stu-id="be595-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="be595-140">`MyDependency2`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="be595-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="be595-141">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="be595-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="be595-142">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="be595-143">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="be595-144">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="be595-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="be595-145">`ILogger<TCategoryName>`to [Usługa udostępniona przez platformę](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="be595-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="be595-146">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="be595-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="be595-147">W terminologii wtrysku zależności, usługa:</span><span class="sxs-lookup"><span data-stu-id="be595-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="be595-148">Jest zazwyczaj obiektem, który dostarcza usługę do innego kodu w aplikacji, takiej jak `IMyDependency` Usługa.</span><span class="sxs-lookup"><span data-stu-id="be595-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="be595-149">Nie jest powiązany z usługą sieci Web, chociaż usługa może korzystać z usługi sieci Web.</span><span class="sxs-lookup"><span data-stu-id="be595-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="be595-150">Struktura zapewnia niezawodny system [rejestrowania](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="be595-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="be595-151">`IMyDependency`Implementacje zostały sporządzone w celu zaprezentowania podstawowej i nie zaimplementowania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="be595-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="be595-152">Większość aplikacji nie musi być w trakcie pisania rejestratorów.</span><span class="sxs-lookup"><span data-stu-id="be595-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="be595-153">Poniższy kod ilustruje użycie domyślnego rejestrowania, które nie wymaga zarejestrowania żadnych usług w programie `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="be595-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="be595-154">Korzystając z powyższego kodu, nie ma potrzeby aktualizowania, `ConfigureServices` ponieważ [Rejestrowanie](xref:fundamentals/logging/index) jest dostarczane przez strukturę:</span><span class="sxs-lookup"><span data-stu-id="be595-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="be595-155">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="be595-155">Services injected into Startup</span></span>

<span data-ttu-id="be595-156">Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="be595-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="be595-157">Usługi można wstrzyknąć do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="be595-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="be595-158">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup> i [Konfigurowanie dostępu podczas uruchamiania](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="be595-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="be595-159">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="be595-159">Register additional services with extension methods</span></span>

<span data-ttu-id="be595-160">Gdy jest dostępna metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi:</span><span class="sxs-lookup"><span data-stu-id="be595-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="be595-161">Konwencją jest użycie pojedynczej `Add{SERVICE_NAME}` metody rozszerzenia w celu zarejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="be595-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="be595-162">Usługi zależne są również zarejestrowane.</span><span class="sxs-lookup"><span data-stu-id="be595-162">The dependent services are also registered.</span></span>

<span data-ttu-id="be595-163">Poniższy kod jest generowany przez Razor szablon stron przy użyciu poszczególnych kont użytkowników i pokazuje, jak dodać dodatkowe usługi do kontenera przy użyciu metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="be595-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="be595-164">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> i <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="be595-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="be595-165">Zapoznaj się z sekcją [łączenie kolekcji usług](#csc) , aby uzyskać instrukcje dotyczące pisania metody rozszerzenia w celu zarejestrowania usług.</span><span class="sxs-lookup"><span data-stu-id="be595-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="be595-166">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="be595-166">Service lifetimes</span></span>

<span data-ttu-id="be595-167">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="be595-168">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="be595-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="be595-169">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-169">Transient</span></span>

<span data-ttu-id="be595-170">Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="be595-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="be595-171">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="be595-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="be595-172">W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="be595-173">Zakresie</span><span class="sxs-lookup"><span data-stu-id="be595-173">Scoped</span></span>

<span data-ttu-id="be595-174">Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="be595-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="be595-175">W przypadku korzystania z Entity Framework Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Metoda rozszerzenia rejestruje `DbContext` typy z okresem istnienia w zakresie domyślnie.</span><span class="sxs-lookup"><span data-stu-id="be595-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="be595-176">W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="be595-177">Korzystanie z usług objętych zakresem w oprogramowaniu pośredniczącym z jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="be595-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="be595-178">Wsuń usługę do `Invoke` `InvokeAsync` metody lub.</span><span class="sxs-lookup"><span data-stu-id="be595-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="be595-179">Wstrzyknięcie przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) zgłasza wyjątek w czasie wykonywania, ponieważ wymusza zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="be595-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="be595-180">Przykład w [opcjach okres istnienia i rejestracji](#lifetime-and-registration-options) używa `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="be595-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="be595-181">[Oprogramowanie pośredniczące oparte na fabryce](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="be595-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="be595-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozszerzające sprawdzają, czy typ zarejestrowanego oprogramowania pośredniczącego jest zaimplementowany <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="be595-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="be595-183">W <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> takim przypadku wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji.</span><span class="sxs-lookup"><span data-stu-id="be595-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="be595-184">Oprogramowanie pośredniczące jest rejestrowane jako usługa w zakresie lub przejściowym w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="be595-185">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="be595-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="be595-186">***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="be595-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="be595-187">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="be595-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="be595-188">Warto:</span><span class="sxs-lookup"><span data-stu-id="be595-188">It's fine to:</span></span>

* <span data-ttu-id="be595-189">Rozwiązanie pojedynczej usługi z usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="be595-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="be595-190">Rozwiąż usługę objętą zakresem z innej usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="be595-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="be595-191">Domyślnie w środowisku programistycznym rozpoznawanie usługi z innej usługi z dłuższym okresem istnienia zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="be595-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="be595-192">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#sv).</span><span class="sxs-lookup"><span data-stu-id="be595-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="be595-193">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-193">Singleton</span></span>

<span data-ttu-id="be595-194">Są tworzone pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ):</span><span class="sxs-lookup"><span data-stu-id="be595-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="be595-195">Podczas pierwszego żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-195">The first time they're requested.</span></span>
* <span data-ttu-id="be595-196">Przez dewelopera, gdy udostępnia wystąpienie implementacji bezpośrednio do kontenera.</span><span class="sxs-lookup"><span data-stu-id="be595-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="be595-197">Takie podejście jest rzadko niezbędne.</span><span class="sxs-lookup"><span data-stu-id="be595-197">This approach is rarely needed.</span></span>

<span data-ttu-id="be595-198">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="be595-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="be595-199">Jeśli aplikacja wymaga pojedynczych zachowań, zezwól kontenerowi usługi na zarządzanie okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="be595-200">Nie Wdrażaj wzorca projektu singleton i podawanie kodu do usuwania pojedynczych elementów.</span><span class="sxs-lookup"><span data-stu-id="be595-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="be595-201">Usługi nigdy nie powinny być usuwane przez kod, który rozwiązał usługę z kontenera.</span><span class="sxs-lookup"><span data-stu-id="be595-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="be595-202">Jeśli typ lub fabryka są zarejestrowane jako pojedyncze, kontener automatycznie usunie pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="be595-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="be595-203">Usługi pojedyncze muszą być bezpieczne dla wątków i często używane w usługach bezstanowych.</span><span class="sxs-lookup"><span data-stu-id="be595-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="be595-204">W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="be595-205">Ponieważ pamięć nie jest wydawana do momentu wyłączenia aplikacji, należy rozważyć użycie pamięci z pojedynczą.</span><span class="sxs-lookup"><span data-stu-id="be595-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="be595-206">***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="be595-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="be595-207">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="be595-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="be595-208">Rozwiązaniem jest rozwiązanie pojedynczej usługi z poziomu usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="be595-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="be595-209">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="be595-209">Service registration methods</span></span>

<span data-ttu-id="be595-210">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="be595-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="be595-211">Metoda</span><span class="sxs-lookup"><span data-stu-id="be595-211">Method</span></span> | <span data-ttu-id="be595-212">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="be595-212">Automatic</span></span><br><span data-ttu-id="be595-213">object</span><span class="sxs-lookup"><span data-stu-id="be595-213">object</span></span><br><span data-ttu-id="be595-214">myśl</span><span class="sxs-lookup"><span data-stu-id="be595-214">disposal</span></span> | <span data-ttu-id="be595-215">Wiele</span><span class="sxs-lookup"><span data-stu-id="be595-215">Multiple</span></span><br><span data-ttu-id="be595-216">implementacje</span><span class="sxs-lookup"><span data-stu-id="be595-216">implementations</span></span> | <span data-ttu-id="be595-217">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="be595-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="be595-218">Przykład:</span><span class="sxs-lookup"><span data-stu-id="be595-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="be595-219">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-219">Yes</span></span> | <span data-ttu-id="be595-220">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-220">Yes</span></span> | <span data-ttu-id="be595-221">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="be595-222">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="be595-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="be595-223">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-223">Yes</span></span> | <span data-ttu-id="be595-224">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-224">Yes</span></span> | <span data-ttu-id="be595-225">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="be595-226">Przykład:</span><span class="sxs-lookup"><span data-stu-id="be595-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="be595-227">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-227">Yes</span></span> | <span data-ttu-id="be595-228">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-228">No</span></span> | <span data-ttu-id="be595-229">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="be595-230">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="be595-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="be595-231">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-231">No</span></span> | <span data-ttu-id="be595-232">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-232">Yes</span></span> | <span data-ttu-id="be595-233">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="be595-234">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="be595-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="be595-235">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-235">No</span></span> | <span data-ttu-id="be595-236">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-236">No</span></span> | <span data-ttu-id="be595-237">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-237">Yes</span></span> |

<span data-ttu-id="be595-238">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="be595-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="be595-239">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="be595-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="be595-240">`TryAdd{LIFETIME}`Metody rejestrują usługę, jeśli nie została jeszcze zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="be595-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="be595-241">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="be595-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="be595-242">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="be595-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="be595-243">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="be595-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="be595-244">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę, jeśli nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="be595-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="be595-245">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="be595-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="be595-246">Podczas rejestrowania usług deweloper powinien dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="be595-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="be595-247">Zazwyczaj autorzy biblioteki używają, `TryAddEnumerable` Aby uniknąć rejestrowania wielu kopii implementacji w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="be595-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="be595-248">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="be595-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="be595-249">Drugi wiersz rejestruje `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="be595-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="be595-250">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="be595-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="be595-251">Rejestracja usługi jest zazwyczaj kolejnością niezależną, z wyjątkiem rejestracji wielu implementacji tego samego typu.</span><span class="sxs-lookup"><span data-stu-id="be595-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="be595-252">`IServiceCollection`jest kolekcją <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="be595-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="be595-253">Poniższy kod pokazuje, jak dodać usługę z konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="be595-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="be595-254">`Add{LIFETIME}`Metody te używają tego samego podejścia.</span><span class="sxs-lookup"><span data-stu-id="be595-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="be595-255">Na przykład zapoznaj się z [kodem źródłowym i Addscoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="be595-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="be595-256">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="be595-256">Constructor injection behavior</span></span>

<span data-ttu-id="be595-257">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="be595-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="be595-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="be595-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="be595-259">Tworzy obiekty bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="be595-260">Używany z funkcjami platformy, takimi jak [pomocnicy tagów](xref:mvc/views/tag-helpers/intro), kontrolery MVC i [powiązania modeli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="be595-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="be595-261">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="be595-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="be595-262">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="be595-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="be595-263">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="be595-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="be595-264">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="be595-265">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="be595-265">Entity Framework contexts</span></span>

<span data-ttu-id="be595-266">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="be595-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="be595-267">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="be595-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="be595-268">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="be595-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="be595-269">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="be595-269">Lifetime and registration options</span></span>

<span data-ttu-id="be595-270">Aby zademonstrować różnicę między opcjami okresu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="be595-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="be595-271">W zależności od sposobu skonfigurowania okresu istnienia usługi dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="be595-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="be595-272">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="be595-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="be595-273">`Operation`Konstruktor generuje ostatnie 4 znaki identyfikatora GUID, jeśli jeden nie został podany:</span><span class="sxs-lookup"><span data-stu-id="be595-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="be595-274">W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="be595-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="be595-275">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="be595-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="be595-276">Aplikacja Przykładowa `IndexModel` i oprogramowanie pośredniczące żąda każdego rodzaju `IOperation` typu i rejestruje `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="be595-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="be595-277">Oprogramowanie pośredniczące jest podobne do `IndexModel` i rozwiązuje te same usługi:</span><span class="sxs-lookup"><span data-stu-id="be595-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="be595-278">Usługi w zakresie muszą zostać rozwiązane w `InvokeAsync` metodzie:</span><span class="sxs-lookup"><span data-stu-id="be595-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="be595-279">Dane wyjściowe rejestratora przedstawiają:</span><span class="sxs-lookup"><span data-stu-id="be595-279">The logger output shows:</span></span>

* <span data-ttu-id="be595-280">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="be595-280">*Transient* objects are always different.</span></span> <span data-ttu-id="be595-281">Wartość przejściowa `OperationId` jest różna w oprogramowaniu i oprogramowaniu `IndexModel` pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="be595-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="be595-282">Obiekty w *zakresie* są takie same w poszczególnych żądaniach, ale różnią się w zależności od każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="be595-283">*Pojedyncze* obiekty są takie same dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="be595-284">Aby zmniejszyć ilość danych wyjściowych rejestrowania, ustaw wartość opcji "Rejestrowanie: LogLevel: Microsoft: Error" w *appsettings.Development.js* w pliku:</span><span class="sxs-lookup"><span data-stu-id="be595-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="be595-285">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="be595-285">Call services from main</span></span>

<span data-ttu-id="be595-286">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="be595-287">Takie podejście jest przydatne w celu uzyskania dostępu do usługi o określonym zakresie podczas uruchamiania, aby uruchomić zadania inicjowania:</span><span class="sxs-lookup"><span data-stu-id="be595-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="be595-288">Poprzedni kod pochodzi od [dodania inicjatora inicjatora](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) w Razor samouczku strony.</span><span class="sxs-lookup"><span data-stu-id="be595-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="be595-289">Poniższy przykład pokazuje, jak uzyskać kontekst dla `IMyDependency` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="be595-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="be595-290">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="be595-290">Scope validation</span></span>

<span data-ttu-id="be595-291">Gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments) i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do skompilowania hosta, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="be595-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="be595-292">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="be595-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="be595-293">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="be595-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="be595-294">Usługi przejściowe nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych lub usług objętych zakresem.</span><span class="sxs-lookup"><span data-stu-id="be595-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="be595-295">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="be595-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="be595-296">Okres istnienia dostawcy usług głównych odpowiada okresowi istnienia aplikacji, gdy dostawca rozpoczyna pracę z aplikacją i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="be595-297">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="be595-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="be595-298">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja jest wyłączona.</span><span class="sxs-lookup"><span data-stu-id="be595-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="be595-299">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="be595-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="be595-300">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="be595-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="be595-301">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="be595-301">Request Services</span></span>

<span data-ttu-id="be595-302">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="be595-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="be595-303">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="be595-304">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="be595-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="be595-305">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="be595-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="be595-306">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól platformie na wstrzyknięcie zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="be595-307">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="be595-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="be595-308">ASP.NET Core tworzy zakres na żądanie i `RequestServices` uwidacznia dostawcę usług objętych zakresem.</span><span class="sxs-lookup"><span data-stu-id="be595-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="be595-309">Wszystkie usługi objęte zakresem są prawidłowe przez cały czas, gdy żądanie jest aktywne.</span><span class="sxs-lookup"><span data-stu-id="be595-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="be595-310">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="be595-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="be595-311">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="be595-311">Design services for dependency injection</span></span>

<span data-ttu-id="be595-312">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="be595-312">Best practices are to:</span></span>

* <span data-ttu-id="be595-313">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="be595-314">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="be595-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="be595-315">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="be595-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="be595-316">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="be595-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="be595-317">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="be595-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="be595-318">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="be595-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="be595-319">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="be595-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="be595-320">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="be595-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="be595-321">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="be595-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="be595-322">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="be595-322">Disposal of services</span></span>

<span data-ttu-id="be595-323">Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="be595-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="be595-324">Usługi nigdy nie powinny być usuwane przez żaden kod, który rozwiązał usługę z kontenera.</span><span class="sxs-lookup"><span data-stu-id="be595-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="be595-325">Jeśli typ lub fabryka są rejestrowane jako pojedyncze, kontener usuwa pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="be595-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="be595-326">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="be595-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="be595-327">Konsola debugowania pokazuje następujące dane wyjściowe po każdym odświeżeniu strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="be595-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="be595-328">Usługi, które nie zostały utworzone przez kontener usługi</span><span class="sxs-lookup"><span data-stu-id="be595-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="be595-329">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="be595-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="be595-330">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="be595-330">In the preceding code:</span></span>

* <span data-ttu-id="be595-331">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="be595-332">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="be595-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="be595-333">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="be595-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="be595-334">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="be595-335">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="be595-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="be595-336">Przejściowy, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-336">Transient, limited lifetime</span></span>

<span data-ttu-id="be595-337">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="be595-337">**Scenario**</span></span>

<span data-ttu-id="be595-338">Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="be595-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="be595-339">Wystąpienie jest rozwiązane w zakresie głównym (kontener główny).</span><span class="sxs-lookup"><span data-stu-id="be595-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="be595-340">Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.</span><span class="sxs-lookup"><span data-stu-id="be595-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="be595-341">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="be595-341">**Solution**</span></span>

<span data-ttu-id="be595-342">Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="be595-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="be595-343">W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego.</span><span class="sxs-lookup"><span data-stu-id="be595-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="be595-344">Jeśli typ końcowy ma inne zależności, fabryka może:</span><span class="sxs-lookup"><span data-stu-id="be595-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="be595-345">Odbierz <xref:System.IServiceProvider> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="be595-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="be595-346">Użyj, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="be595-347">Wystąpienie udostępnione, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="be595-348">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="be595-348">**Scenario**</span></span>

<span data-ttu-id="be595-349">Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> powinna mieć ograniczony okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="be595-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="be595-350">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="be595-350">**Solution**</span></span>

<span data-ttu-id="be595-351">Zarejestruj wystąpienie z okresem istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="be595-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="be595-352">Użyj, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> Aby rozpocząć i utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="be595-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="be595-353">Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="be595-354">Usuń zakres, gdy okres istnienia powinien zostać zakończony.</span><span class="sxs-lookup"><span data-stu-id="be595-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="be595-355">Ogólne wytyczne interfejsu IDisposable</span><span class="sxs-lookup"><span data-stu-id="be595-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="be595-356">Nie rejestruj <xref:System.IDisposable> wystąpień z zakresem przejściowym.</span><span class="sxs-lookup"><span data-stu-id="be595-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="be595-357">Zamiast tego użyj wzorca fabryki.</span><span class="sxs-lookup"><span data-stu-id="be595-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="be595-358">Nie należy rozwiązywać wystąpień przejściowych lub zakresów <xref:System.IDisposable> w zakresie w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="be595-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="be595-359">Jedyny ogólny wyjątek polega na tym, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> obiekt, który nie jest idealnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="be595-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="be595-360">Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie.</span><span class="sxs-lookup"><span data-stu-id="be595-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="be595-361">Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="be595-362">Zakresy powinny służyć do kontrolowania okresów istnienia usług.</span><span class="sxs-lookup"><span data-stu-id="be595-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="be595-363">Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.</span><span class="sxs-lookup"><span data-stu-id="be595-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="be595-364">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="be595-364">Default service container replacement</span></span>

<span data-ttu-id="be595-365">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="be595-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="be595-366">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="be595-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="be595-367">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="be595-367">Property injection</span></span>
* <span data-ttu-id="be595-368">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="be595-368">Injection based on name</span></span>
* <span data-ttu-id="be595-369">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="be595-369">Child containers</span></span>
* <span data-ttu-id="be595-370">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-370">Custom lifetime management</span></span>
* <span data-ttu-id="be595-371">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="be595-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="be595-372">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="be595-372">Convention-based registration</span></span>

<span data-ttu-id="be595-373">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="be595-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="be595-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="be595-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="be595-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="be595-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="be595-376">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="be595-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="be595-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="be595-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="be595-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="be595-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="be595-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="be595-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="be595-380">Unity</span><span class="sxs-lookup"><span data-stu-id="be595-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="be595-381">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="be595-381">Thread safety</span></span>

<span data-ttu-id="be595-382">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="be595-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="be595-383">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="be595-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="be595-384">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="be595-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="be595-385">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="be595-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="be595-386">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="be595-386">Recommendations</span></span>

* <span data-ttu-id="be595-387">`async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="be595-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="be595-388">Język C# nie obsługuje konstruktorów asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="be595-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="be595-389">Zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="be595-390">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="be595-391">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="be595-392">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="be595-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="be595-393">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="be595-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="be595-394">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="be595-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="be595-395">Unikaj statycznego dostępu do usług.</span><span class="sxs-lookup"><span data-stu-id="be595-395">Avoid static access to services.</span></span> <span data-ttu-id="be595-396">Na przykład należy unikać statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).</span><span class="sxs-lookup"><span data-stu-id="be595-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="be595-397">Utrzymuj szybkie i synchroniczne operacje.</span><span class="sxs-lookup"><span data-stu-id="be595-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="be595-398">Unikaj używania *wzorca lokalizatora usługi*.</span><span class="sxs-lookup"><span data-stu-id="be595-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="be595-399">Na przykład nie wywołuj, <xref:System.IServiceProvider.GetService*> Aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="be595-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="be595-400">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="be595-400">**Incorrect:**</span></span>

    ![Niepoprawny kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="be595-402">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="be595-402">**Correct**:</span></span>

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
* <span data-ttu-id="be595-403">Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="be595-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="be595-404">Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="be595-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="be595-405">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="be595-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="be595-406">Należy unikać wywołań do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="be595-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="be595-407">Wywoływanie `BuildServiceProvider` zazwyczaj odbywa się, gdy deweloper chce rozwiązać usługę w programie `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="be595-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="be595-408">Rozważmy na przykład przypadek, w którym musisz się zapoznać `LoginPath` z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="be595-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="be595-409">Unikaj następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="be595-409">Avoid the following code:</span></span>

  ![zły kod wywołujący BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="be595-411">Na powyższym obrazie, wybierając zieloną linię falistą w obszarze `services.BuildServiceProvider` pokazuje następujące ostrzeżenie ASP0000:</span><span class="sxs-lookup"><span data-stu-id="be595-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="be595-412">ASP0000 wywołanie "BuildServiceProvider" z kodu aplikacji spowoduje utworzenie dodatkowej kopii pojedynczych usług.</span><span class="sxs-lookup"><span data-stu-id="be595-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="be595-413">Należy rozważyć alternatywy, takie jak "wstrzyknięcie usług" jako parametry do "configure".</span><span class="sxs-lookup"><span data-stu-id="be595-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="be595-414">Wywołanie `BuildServiceProvider` tworzy drugi kontener, który może tworzyć niezerwane pojedyncze i spowodować odwołania do grafów obiektów w wielu kontenerach.</span><span class="sxs-lookup"><span data-stu-id="be595-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="be595-415">Prawidłowym sposobem uzyskania `LoginPath` jest użycie wzorca opcji z opcją di:</span><span class="sxs-lookup"><span data-stu-id="be595-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="be595-416">Nierozporządzalne usługi przejściowe są przechwytywane przez kontener do usuwania.</span><span class="sxs-lookup"><span data-stu-id="be595-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="be595-417">Może to obsłużyć przeciek pamięci, jeśli został rozwiązany z kontenera najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="be595-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="be595-418">Włącz weryfikację zakresu, aby upewnić się, że aplikacja nie ma usług objętych zakresem przechwytujących pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="be595-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="be595-419">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="be595-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="be595-420">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="be595-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="be595-421">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="be595-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="be595-422">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="be595-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="be595-423">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="be595-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="be595-424">Zalecane wzorce dla wielu dzierżawców w programie DI</span><span class="sxs-lookup"><span data-stu-id="be595-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="be595-425">[Podstawowe funkcje sadu](https://github.com/OrchardCMS/OrchardCore) zapewniają wiele dzierżawców.</span><span class="sxs-lookup"><span data-stu-id="be595-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="be595-426">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją podstawową programu sadu](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="be595-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="be595-427">Zapoznaj się z https://github.com/OrchardCMS/OrchardCore.Samples przykładowymi aplikacjami, aby poznać Przykłady sposobu tworzenia aplikacji modularnych i wielodostępnych przy użyciu samej platformy sadu Core bez żadnej z określonych funkcji usługi CMS.</span><span class="sxs-lookup"><span data-stu-id="be595-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="be595-428">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="be595-428">Framework-provided services</span></span>

<span data-ttu-id="be595-429">`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="be595-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="be595-430">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="be595-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="be595-431">Aplikacje oparte na szablonach ASP.NET Core mają więcej niż 250 usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="be595-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="be595-432">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="be595-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="be595-433">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="be595-433">Service Type</span></span> | <span data-ttu-id="be595-434">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="be595-435">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="be595-436">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="be595-437">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="be595-438">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="be595-439">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="be595-440">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="be595-441">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="be595-442">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="be595-443">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="be595-444">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="be595-445">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="be595-446">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="be595-447">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="be595-448">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="be595-449">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="be595-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="be595-450">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be595-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="be595-451">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="be595-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="be595-452">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="be595-452">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="be595-453">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="be595-453">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="be595-454">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="be595-454">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be595-455">[Steve Kowalski](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="be595-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="be595-456">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="be595-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="be595-457">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="be595-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="be595-458">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be595-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="be595-459">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="be595-459">Overview of dependency injection</span></span>

<span data-ttu-id="be595-460">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="be595-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="be595-461">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="be595-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="be595-462">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="be595-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="be595-463">`MyDependency`Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="be595-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="be595-464">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="be595-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="be595-465">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="be595-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="be595-466">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="be595-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="be595-467">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="be595-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="be595-468">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="be595-469">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="be595-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="be595-470">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="be595-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="be595-471">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="be595-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="be595-472">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="be595-473">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="be595-474">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="be595-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="be595-475">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="be595-476">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="be595-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="be595-477">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="be595-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="be595-478">W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="be595-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="be595-479">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="be595-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="be595-480">`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="be595-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="be595-481">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="be595-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="be595-482">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="be595-483">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="be595-484">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="be595-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="be595-485">`IMyDependency`i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="be595-486">`IMyDependency`jest zarejestrowany w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="be595-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="be595-487">`ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="be595-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="be595-488">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="be595-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="be595-489">W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="be595-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="be595-490">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="be595-491">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="be595-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="be595-492">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="be595-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="be595-493">Na przykład `services.AddMvc()` dodaje Razor strony usług i wymagane przez MVC.</span><span class="sxs-lookup"><span data-stu-id="be595-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="be595-494">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="be595-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="be595-495">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="be595-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="be595-496">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string` ,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="be595-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="be595-497">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="be595-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="be595-498">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="be595-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="be595-499">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="be595-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="be595-500">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="be595-500">Services injected into Startup</span></span>

<span data-ttu-id="be595-501">Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="be595-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="be595-502">Usługi można wstrzyknąć do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="be595-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="be595-503">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="be595-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="be595-504">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="be595-504">Framework-provided services</span></span>

<span data-ttu-id="be595-505">`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="be595-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="be595-506">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="be595-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="be595-507">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="be595-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="be595-508">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="be595-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="be595-509">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="be595-509">Service Type</span></span> | <span data-ttu-id="be595-510">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="be595-511">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="be595-512">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="be595-513">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="be595-514">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="be595-515">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="be595-516">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="be595-517">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="be595-518">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="be595-519">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="be595-520">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="be595-521">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="be595-522">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="be595-523">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="be595-524">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="be595-525">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="be595-525">Register additional services with extension methods</span></span>

<span data-ttu-id="be595-526">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać pojedynczej `Add{SERVICE_NAME}` metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="be595-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="be595-527">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="be595-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="be595-528">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="be595-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="be595-529">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="be595-529">Service lifetimes</span></span>

<span data-ttu-id="be595-530">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="be595-531">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="be595-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="be595-532">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="be595-532">Transient</span></span>

<span data-ttu-id="be595-533">Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="be595-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="be595-534">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="be595-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="be595-535">W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="be595-536">Zakresie</span><span class="sxs-lookup"><span data-stu-id="be595-536">Scoped</span></span>

<span data-ttu-id="be595-537">Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="be595-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="be595-538">W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="be595-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="be595-539">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` `InvokeAsync` metody lub.</span><span class="sxs-lookup"><span data-stu-id="be595-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="be595-540">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="be595-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="be595-541">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="be595-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="be595-542">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="be595-542">Singleton</span></span>

<span data-ttu-id="be595-543">Pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="be595-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="be595-544">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="be595-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="be595-545">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="be595-546">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="be595-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="be595-547">W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="be595-548">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="be595-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="be595-549">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="be595-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="be595-550">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="be595-550">Service registration methods</span></span>

<span data-ttu-id="be595-551">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="be595-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="be595-552">Metoda</span><span class="sxs-lookup"><span data-stu-id="be595-552">Method</span></span> | <span data-ttu-id="be595-553">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="be595-553">Automatic</span></span><br><span data-ttu-id="be595-554">object</span><span class="sxs-lookup"><span data-stu-id="be595-554">object</span></span><br><span data-ttu-id="be595-555">myśl</span><span class="sxs-lookup"><span data-stu-id="be595-555">disposal</span></span> | <span data-ttu-id="be595-556">Wiele</span><span class="sxs-lookup"><span data-stu-id="be595-556">Multiple</span></span><br><span data-ttu-id="be595-557">implementacje</span><span class="sxs-lookup"><span data-stu-id="be595-557">implementations</span></span> | <span data-ttu-id="be595-558">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="be595-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="be595-559">Przykład:</span><span class="sxs-lookup"><span data-stu-id="be595-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="be595-560">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-560">Yes</span></span> | <span data-ttu-id="be595-561">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-561">Yes</span></span> | <span data-ttu-id="be595-562">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="be595-563">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="be595-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="be595-564">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-564">Yes</span></span> | <span data-ttu-id="be595-565">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-565">Yes</span></span> | <span data-ttu-id="be595-566">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="be595-567">Przykład:</span><span class="sxs-lookup"><span data-stu-id="be595-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="be595-568">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-568">Yes</span></span> | <span data-ttu-id="be595-569">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-569">No</span></span> | <span data-ttu-id="be595-570">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="be595-571">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="be595-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="be595-572">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-572">No</span></span> | <span data-ttu-id="be595-573">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-573">Yes</span></span> | <span data-ttu-id="be595-574">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="be595-575">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="be595-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="be595-576">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-576">No</span></span> | <span data-ttu-id="be595-577">Nie</span><span class="sxs-lookup"><span data-stu-id="be595-577">No</span></span> | <span data-ttu-id="be595-578">Tak</span><span class="sxs-lookup"><span data-stu-id="be595-578">Yes</span></span> |

<span data-ttu-id="be595-579">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="be595-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="be595-580">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="be595-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="be595-581">`TryAdd{LIFETIME}`Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="be595-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="be595-582">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="be595-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="be595-583">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="be595-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="be595-584">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="be595-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="be595-585">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="be595-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="be595-586">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="be595-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="be595-587">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="be595-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="be595-588">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="be595-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="be595-589">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="be595-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="be595-590">Drugi wiersz rejestruje `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="be595-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="be595-591">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="be595-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="be595-592">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="be595-592">Constructor injection behavior</span></span>

<span data-ttu-id="be595-593">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="be595-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="be595-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="be595-595">`ActivatorUtilities`jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="be595-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="be595-596">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="be595-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="be595-597">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="be595-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="be595-598">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="be595-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="be595-599">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="be595-600">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="be595-600">Entity Framework contexts</span></span>

<span data-ttu-id="be595-601">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="be595-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="be595-602">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="be595-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="be595-603">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="be595-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="be595-604">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="be595-604">Lifetime and registration options</span></span>

<span data-ttu-id="be595-605">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z unikatowym identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="be595-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="be595-606">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="be595-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="be595-607">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="be595-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="be595-608">`Operation`Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="be595-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="be595-609">`OperationService`Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="be595-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="be595-610">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="be595-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="be595-611">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` Usługa różni `IOperationTransient` się od `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="be595-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="be595-612">`OperationService`odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="be595-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="be595-613">Nowe wystąpienie daje inną wartość `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="be595-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="be595-614">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` Usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="be595-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="be595-615">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="be595-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="be595-616">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="be595-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="be595-617">W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="be595-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="be595-618">`IOperationSingletonInstance`Usługa używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="be595-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="be595-619">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="be595-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="be595-620">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="be595-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="be595-621">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="be595-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="be595-622">Następnie na stronie zostaną wyświetlone wszystkie wartości klasy modelu strony i usługi `OperationId` za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="be595-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="be595-623">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="be595-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="be595-624">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="be595-624">**First request:**</span></span>

<span data-ttu-id="be595-625">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="be595-625">Controller operations:</span></span>

<span data-ttu-id="be595-626">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="be595-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="be595-627">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="be595-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="be595-628">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be595-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be595-629">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be595-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be595-630">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="be595-630">`OperationService` operations:</span></span>

<span data-ttu-id="be595-631">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="be595-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="be595-632">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="be595-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="be595-633">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be595-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be595-634">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be595-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be595-635">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="be595-635">**Second request:**</span></span>

<span data-ttu-id="be595-636">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="be595-636">Controller operations:</span></span>

<span data-ttu-id="be595-637">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="be595-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="be595-638">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="be595-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="be595-639">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be595-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be595-640">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be595-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be595-641">`OperationService`składowa</span><span class="sxs-lookup"><span data-stu-id="be595-641">`OperationService` operations:</span></span>

<span data-ttu-id="be595-642">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="be595-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="be595-643">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="be595-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="be595-644">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be595-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be595-645">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be595-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be595-646">Zauważ, że `OperationId` wartości różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="be595-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="be595-647">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="be595-647">*Transient* objects are always different.</span></span> <span data-ttu-id="be595-648">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="be595-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="be595-649">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="be595-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="be595-650">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="be595-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="be595-651">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, czy `Operation` wystąpienie jest dostarczone w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="be595-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="be595-652">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="be595-652">Call services from main</span></span>

<span data-ttu-id="be595-653">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="be595-654">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="be595-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="be595-655">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="be595-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="be595-656">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="be595-656">Scope validation</span></span>

<span data-ttu-id="be595-657">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="be595-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="be595-658">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="be595-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="be595-659">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="be595-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="be595-660">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="be595-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="be595-661">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="be595-662">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="be595-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="be595-663">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="be595-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="be595-664">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="be595-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="be595-665">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="be595-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="be595-666">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="be595-666">Request Services</span></span>

<span data-ttu-id="be595-667">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="be595-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="be595-668">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="be595-669">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="be595-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="be595-670">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="be595-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="be595-671">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="be595-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="be595-672">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="be595-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="be595-673">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="be595-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="be595-674">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="be595-674">Design services for dependency injection</span></span>

<span data-ttu-id="be595-675">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="be595-675">Best practices are to:</span></span>

* <span data-ttu-id="be595-676">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="be595-677">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="be595-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="be595-678">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="be595-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="be595-679">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="be595-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="be595-680">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="be595-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="be595-681">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="be595-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="be595-682">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="be595-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="be595-683">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="be595-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="be595-684">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="be595-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="be595-685">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="be595-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="be595-686">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="be595-686">Disposal of services</span></span>

<span data-ttu-id="be595-687">Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="be595-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="be595-688">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="be595-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="be595-689">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="be595-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="be595-690">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="be595-690">In the following example:</span></span>

* <span data-ttu-id="be595-691">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="be595-692">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="be595-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="be595-693">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="be595-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="be595-694">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be595-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="be595-695">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="be595-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="be595-696">Przejściowy, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-696">Transient, limited lifetime</span></span>

<span data-ttu-id="be595-697">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="be595-697">**Scenario**</span></span>

<span data-ttu-id="be595-698">Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="be595-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="be595-699">Wystąpienie jest rozwiązane w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="be595-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="be595-700">Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.</span><span class="sxs-lookup"><span data-stu-id="be595-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="be595-701">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="be595-701">**Solution**</span></span>

<span data-ttu-id="be595-702">Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="be595-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="be595-703">W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego.</span><span class="sxs-lookup"><span data-stu-id="be595-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="be595-704">Jeśli typ końcowy ma inne zależności, fabryka może:</span><span class="sxs-lookup"><span data-stu-id="be595-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="be595-705">Odbierz <xref:System.IServiceProvider> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="be595-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="be595-706">Użyj, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="be595-707">Wystąpienie udostępnione, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="be595-708">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="be595-708">**Scenario**</span></span>

<span data-ttu-id="be595-709">Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> powinna mieć ograniczony okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="be595-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="be595-710">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="be595-710">**Solution**</span></span>

<span data-ttu-id="be595-711">Zarejestruj wystąpienie z okresem istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="be595-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="be595-712">Użyj, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> Aby rozpocząć i utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="be595-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="be595-713">Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="be595-714">Usuń zakres, gdy okres istnienia powinien zostać zakończony.</span><span class="sxs-lookup"><span data-stu-id="be595-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="be595-715">Ogólne wskazówki</span><span class="sxs-lookup"><span data-stu-id="be595-715">General Guidelines</span></span>

* <span data-ttu-id="be595-716">Nie rejestruj <xref:System.IDisposable> wystąpień z zakresem przejściowym.</span><span class="sxs-lookup"><span data-stu-id="be595-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="be595-717">Zamiast tego użyj wzorca fabryki.</span><span class="sxs-lookup"><span data-stu-id="be595-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="be595-718">Nie należy rozwiązywać wystąpień przejściowych lub zakresów <xref:System.IDisposable> w zakresie w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="be595-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="be595-719">Jedyny ogólny wyjątek polega na tym, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> obiekt, który nie jest idealnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="be595-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="be595-720">Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie.</span><span class="sxs-lookup"><span data-stu-id="be595-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="be595-721">Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.</span><span class="sxs-lookup"><span data-stu-id="be595-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="be595-722">Zakresy powinny służyć do kontrolowania okresów istnienia usług.</span><span class="sxs-lookup"><span data-stu-id="be595-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="be595-723">Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.</span><span class="sxs-lookup"><span data-stu-id="be595-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="be595-724">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="be595-724">Default service container replacement</span></span>

<span data-ttu-id="be595-725">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="be595-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="be595-726">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="be595-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="be595-727">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="be595-727">Property injection</span></span>
* <span data-ttu-id="be595-728">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="be595-728">Injection based on name</span></span>
* <span data-ttu-id="be595-729">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="be595-729">Child containers</span></span>
* <span data-ttu-id="be595-730">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="be595-730">Custom lifetime management</span></span>
* <span data-ttu-id="be595-731">`Func<T>`Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="be595-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="be595-732">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="be595-732">Convention-based registration</span></span>

<span data-ttu-id="be595-733">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="be595-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="be595-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="be595-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="be595-735">DryIoc</span><span class="sxs-lookup"><span data-stu-id="be595-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="be595-736">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="be595-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="be595-737">LightInject</span><span class="sxs-lookup"><span data-stu-id="be595-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="be595-738">Lamar</span><span class="sxs-lookup"><span data-stu-id="be595-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="be595-739">Stashbox</span><span class="sxs-lookup"><span data-stu-id="be595-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="be595-740">Unity</span><span class="sxs-lookup"><span data-stu-id="be595-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="be595-741">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="be595-741">Thread safety</span></span>

<span data-ttu-id="be595-742">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="be595-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="be595-743">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="be595-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="be595-744">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="be595-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="be595-745">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="be595-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="be595-746">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="be595-746">Recommendations</span></span>

* <span data-ttu-id="be595-747">`async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="be595-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="be595-748">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="be595-749">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="be595-750">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="be595-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="be595-751">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="be595-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="be595-752">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="be595-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="be595-753">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="be595-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="be595-754">Unikaj statycznego dostępu do usług.</span><span class="sxs-lookup"><span data-stu-id="be595-754">Avoid static access to services.</span></span> <span data-ttu-id="be595-755">Na przykład należy unikać statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu.</span><span class="sxs-lookup"><span data-stu-id="be595-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="be595-756">Unikaj używania *wzorca lokalizatora usługi*, który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="be595-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="be595-757">Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="be595-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="be595-758">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="be595-758">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="be595-759">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="be595-759">**Correct**:</span></span>

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

* <span data-ttu-id="be595-760">Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie wykonywania za pomocą polecenia <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="be595-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="be595-761">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="be595-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="be595-762">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="be595-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="be595-763">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="be595-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="be595-764">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="be595-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="be595-765">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="be595-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be595-766">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="be595-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="be595-767">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be595-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="be595-768">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="be595-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="be595-769">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="be595-769">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="be595-770">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="be595-770">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="be595-771">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="be595-771">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
