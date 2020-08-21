---
title: Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)
author: rick-anderson
description: Dowiedz się, w jaki sposób ASP.NET Core implementuje iniekcję zależności i jak z niej korzystać.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0a51647463362d6cfac335688d42d4be013f8b9c
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712519"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="73c66-103">Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="73c66-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73c66-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="73c66-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="73c66-105">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="73c66-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="73c66-106">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="73c66-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="73c66-107">Aby uzyskać więcej informacji na temat wstrzykiwania zależności opcji, zobacz <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="73c66-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="73c66-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73c66-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="73c66-109">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="73c66-109">Overview of dependency injection</span></span>

<span data-ttu-id="73c66-110">*Zależność* jest obiektem, od którego zależy inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="73c66-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="73c66-111">Zapoznaj się `MyDependency` z następującą klasą z `WriteMessage` metodą, od której zależą inne klasy:</span><span class="sxs-lookup"><span data-stu-id="73c66-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="73c66-112">Klasa może utworzyć wystąpienie `MyDependency` klasy, aby użyć jej `WriteMessage` metody.</span><span class="sxs-lookup"><span data-stu-id="73c66-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="73c66-113">W poniższym przykładzie `MyDependency` Klasa jest zależna od `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="73c66-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="73c66-114">Klasa tworzy i bezpośrednio zależy od `MyDependency` klasy.</span><span class="sxs-lookup"><span data-stu-id="73c66-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="73c66-115">Zależności kodu, takie jak w poprzednim przykładzie, są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="73c66-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="73c66-116">Aby zastąpić `MyDependency` inną implementacją, `IndexModel` należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="73c66-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="73c66-117">Jeśli `MyDependency` ma zależności, muszą one być również konfigurowane przez `IndexModel` klasę.</span><span class="sxs-lookup"><span data-stu-id="73c66-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="73c66-118">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="73c66-119">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="73c66-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="73c66-120">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="73c66-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="73c66-121">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="73c66-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="73c66-122">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="73c66-123">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="73c66-124">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="73c66-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="73c66-125">Usługi są zwykle zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="73c66-126">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="73c66-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="73c66-127">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="73c66-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="73c66-128">W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje `WriteMessage` metodę:</span><span class="sxs-lookup"><span data-stu-id="73c66-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="73c66-129">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="73c66-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="73c66-130">Przykładowa aplikacja rejestruje `IMyDependency` usługę przy użyciu konkretnego typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="73c66-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="73c66-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda rejestruje usługę z okresem istnienia w określonym zakresie, okresem istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="73c66-132">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="73c66-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="73c66-133">W przykładowej aplikacji `IMyDependency` jest wymagana usługa, która jest używana do wywołania `WriteMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="73c66-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="73c66-134">Przy użyciu wzorca DI, kontroler:</span><span class="sxs-lookup"><span data-stu-id="73c66-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="73c66-135">Nie używa konkretnego typu `MyDependency` , tylko `IMyDependency` interfejsu, który implementuje.</span><span class="sxs-lookup"><span data-stu-id="73c66-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="73c66-136">Dzięki temu można łatwo zmienić implementację używaną przez kontroler bez modyfikowania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="73c66-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="73c66-137">Nie tworzy wystąpienia `MyDependency` , jest tworzone przez element di kontener.</span><span class="sxs-lookup"><span data-stu-id="73c66-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="73c66-138">Implementację `IMyDependency` interfejsu można ulepszyć za pomocą wbudowanego interfejsu API rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="73c66-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="73c66-139">Zaktualizowana `ConfigureServices` Metoda rejestruje nową `IMyDependency` implementację:</span><span class="sxs-lookup"><span data-stu-id="73c66-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="73c66-140">`MyDependency2` zależy od <xref:Microsoft.Extensions.Logging.ILogger%601> , który z nich żąda w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="73c66-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="73c66-141">`ILogger<TCategoryName>` to [Usługa udostępniona przez platformę](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="73c66-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="73c66-142">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="73c66-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="73c66-143">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="73c66-144">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="73c66-145">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="73c66-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="73c66-146">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="73c66-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="73c66-147">W terminologii wtrysku zależności, usługa:</span><span class="sxs-lookup"><span data-stu-id="73c66-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="73c66-148">Jest zazwyczaj obiektem, który udostępnia usługę innym obiektom, takim jak `IMyDependency` Usługa.</span><span class="sxs-lookup"><span data-stu-id="73c66-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="73c66-149">Nie jest powiązany z usługą sieci Web, chociaż usługa może korzystać z usługi sieci Web.</span><span class="sxs-lookup"><span data-stu-id="73c66-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="73c66-150">Struktura zapewnia niezawodny system [rejestrowania](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="73c66-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="73c66-151">`IMyDependency`Implementacje opisane w powyższych przykładach zostały opracowane w celu zademonstrowania podstawowych di, a nie do zaimplementowania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="73c66-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="73c66-152">Większość aplikacji nie musi być w trakcie pisania rejestratorów.</span><span class="sxs-lookup"><span data-stu-id="73c66-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="73c66-153">Poniższy kod ilustruje użycie domyślnego rejestrowania, które nie wymaga zarejestrowania żadnych usług w programie `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="73c66-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="73c66-154">Korzystając z powyższego kodu, nie ma potrzeby aktualizowania `ConfigureServices` , ponieważ [Rejestrowanie](xref:fundamentals/logging/index) jest dostarczane przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="73c66-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="73c66-155">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="73c66-155">Services injected into Startup</span></span>

<span data-ttu-id="73c66-156">Usługi można wstrzyknąć do `Startup` konstruktora i `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="73c66-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="73c66-157">Tylko następujące usługi mogą być wstrzykiwane do konstruktora w `Startup` przypadku korzystania z hosta ogólnego ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="73c66-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="73c66-158">Wszystkie usługi zarejestrowane za pomocą DI Container można wstrzyknąć do `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="73c66-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="73c66-159">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup> i [Konfigurowanie dostępu podczas uruchamiania](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="73c66-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="73c66-160">Rejestrowanie grup usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="73c66-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="73c66-161">ASP.NET Core Framework używa konwencji do rejestracji grupy powiązanych usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="73c66-162">Konwencja polega na użyciu pojedynczej `Add{GROUP_NAME}` metody rozszerzenia w celu zarejestrowania wszystkich usług wymaganych przez funkcję platformy.</span><span class="sxs-lookup"><span data-stu-id="73c66-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="73c66-163">Na przykład <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. addcontrollers> Metoda rozszerzenia rejestruje usługi wymagane przez kontrolery MVC.</span><span class="sxs-lookup"><span data-stu-id="73c66-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="73c66-164">Poniższy kod jest generowany przez Razor szablon stron przy użyciu poszczególnych kont użytkowników i pokazuje, jak dodać dodatkowe usługi do kontenera przy użyciu metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="73c66-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="73c66-165">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="73c66-165">Service lifetimes</span></span>

<span data-ttu-id="73c66-166">Usługi mogą być zarejestrowane przy użyciu jednego z następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="73c66-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="73c66-167">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-167">Transient</span></span>
* <span data-ttu-id="73c66-168">Zakresie</span><span class="sxs-lookup"><span data-stu-id="73c66-168">Scoped</span></span>
* <span data-ttu-id="73c66-169">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-169">Singleton</span></span>

<span data-ttu-id="73c66-170">W poniższych sekcjach opisano wszystkie poprzednie okresy istnienia.</span><span class="sxs-lookup"><span data-stu-id="73c66-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="73c66-171">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="73c66-172">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-172">Transient</span></span>

<span data-ttu-id="73c66-173">Przejściowe usługi okresu istnienia są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="73c66-174">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="73c66-175">Zarejestruj usługi przejściowe w usłudze <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="73c66-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="73c66-176">W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="73c66-177">Zakresie</span><span class="sxs-lookup"><span data-stu-id="73c66-177">Scoped</span></span>

<span data-ttu-id="73c66-178">Usługi okresu istnienia w zakresie są tworzone raz dla żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="73c66-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="73c66-179">Zarejestruj usługi z zakresem w usłudze <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="73c66-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="73c66-180">W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="73c66-181">W przypadku korzystania z Entity Framework Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Metoda rozszerzenia rejestruje `DbContext` typy z okresem istnienia w zakresie domyślnie.</span><span class="sxs-lookup"><span data-stu-id="73c66-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="73c66-182">***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="73c66-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="73c66-183">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="73c66-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="73c66-184">Warto:</span><span class="sxs-lookup"><span data-stu-id="73c66-184">It's fine to:</span></span>

* <span data-ttu-id="73c66-185">Rozwiązanie pojedynczej usługi z usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="73c66-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="73c66-186">Rozwiąż usługę objętą zakresem z innej usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="73c66-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="73c66-187">Domyślnie w środowisku programistycznym rozpoznawanie usługi z innej usługi o dłuższym okresie istnienia zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="73c66-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="73c66-188">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#sv).</span><span class="sxs-lookup"><span data-stu-id="73c66-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="73c66-189">Aby korzystać z usług objętych zakresem w oprogramowaniu pośredniczącym, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="73c66-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="73c66-190">Wsuń usługę do programu pośredniczącego `Invoke` lub `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="73c66-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="73c66-191">Użycie [iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) zgłasza wyjątek czasu wykonywania, ponieważ wymusza, aby usługa w zakresie działała jak pojedyncza.</span><span class="sxs-lookup"><span data-stu-id="73c66-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="73c66-192">Przykład w sekcji [Opcje czasu istnienia i rejestracji](#lifetime-and-registration-options) przedstawia `InvokeAsync` podejście.</span><span class="sxs-lookup"><span data-stu-id="73c66-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="73c66-193">Używaj [oprogramowania pośredniczącego opartego na fabryce](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="73c66-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="73c66-194">Oprogramowanie pośredniczące zarejestrowane przy użyciu tego podejścia jest aktywowane na żądanie klienta (połączenie), które umożliwia wprowadzanie w zakresie usług objętych zakresem do metody oprogramowania pośredniczącego `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="73c66-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="73c66-195">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="73c66-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="73c66-196">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-196">Singleton</span></span>

<span data-ttu-id="73c66-197">Są tworzone pojedyncze usługi okresu istnienia:</span><span class="sxs-lookup"><span data-stu-id="73c66-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="73c66-198">Podczas pierwszego żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-198">The first time they're requested.</span></span>
* <span data-ttu-id="73c66-199">Przez dewelopera, gdy udostępnia wystąpienie implementacji bezpośrednio do kontenera.</span><span class="sxs-lookup"><span data-stu-id="73c66-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="73c66-200">Takie podejście jest rzadko niezbędne.</span><span class="sxs-lookup"><span data-stu-id="73c66-200">This approach is rarely needed.</span></span>

<span data-ttu-id="73c66-201">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="73c66-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="73c66-202">Jeśli aplikacja wymaga pojedynczych zachowań, zezwól kontenerowi usługi na zarządzanie okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="73c66-203">Nie Wdrażaj wzorca projektu singleton i podawanie kodu do usuwania pojedynczych elementów.</span><span class="sxs-lookup"><span data-stu-id="73c66-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="73c66-204">Usługi nigdy nie powinny być usuwane przez kod, który rozwiązał usługę z kontenera.</span><span class="sxs-lookup"><span data-stu-id="73c66-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="73c66-205">Jeśli typ lub fabryka są zarejestrowane jako pojedyncze, kontener usuwa pojedyncze automatycznie.</span><span class="sxs-lookup"><span data-stu-id="73c66-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="73c66-206">Zarejestruj usługi pojedyncze przy użyciu programu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="73c66-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="73c66-207">Usługi pojedyncze muszą być bezpieczne dla wątków i często używane w usługach bezstanowych.</span><span class="sxs-lookup"><span data-stu-id="73c66-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="73c66-208">W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="73c66-209">Ponieważ pamięć nie jest wydawana do momentu wyłączenia aplikacji, należy rozważyć użycie pamięci w ramach pojedynczej usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="73c66-210">***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="73c66-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="73c66-211">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="73c66-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="73c66-212">Rozwiązaniem jest rozwiązanie pojedynczej usługi z poziomu usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="73c66-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="73c66-213">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="73c66-213">Service registration methods</span></span>

<span data-ttu-id="73c66-214">Struktura zawiera metody rozszerzenia rejestracji usług, które są przydatne w określonych scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="73c66-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="73c66-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="73c66-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="73c66-216">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="73c66-216">Automatic</span></span><br><span data-ttu-id="73c66-217">object</span><span class="sxs-lookup"><span data-stu-id="73c66-217">object</span></span><br><span data-ttu-id="73c66-218">myśl</span><span class="sxs-lookup"><span data-stu-id="73c66-218">disposal</span></span> | <span data-ttu-id="73c66-219">Wiele</span><span class="sxs-lookup"><span data-stu-id="73c66-219">Multiple</span></span><br><span data-ttu-id="73c66-220">implementacje</span><span class="sxs-lookup"><span data-stu-id="73c66-220">implementations</span></span> | <span data-ttu-id="73c66-221">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="73c66-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="73c66-222">Przykład:</span><span class="sxs-lookup"><span data-stu-id="73c66-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="73c66-223">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-223">Yes</span></span>                             | <span data-ttu-id="73c66-224">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-224">Yes</span></span>                         | <span data-ttu-id="73c66-225">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="73c66-226">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="73c66-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="73c66-227">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-227">Yes</span></span>                             | <span data-ttu-id="73c66-228">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-228">Yes</span></span>                         | <span data-ttu-id="73c66-229">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="73c66-230">Przykład:</span><span class="sxs-lookup"><span data-stu-id="73c66-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="73c66-231">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-231">Yes</span></span>                             | <span data-ttu-id="73c66-232">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-232">No</span></span>                          | <span data-ttu-id="73c66-233">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="73c66-234">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="73c66-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="73c66-235">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-235">No</span></span>                              | <span data-ttu-id="73c66-236">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-236">Yes</span></span>                         | <span data-ttu-id="73c66-237">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="73c66-238">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="73c66-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="73c66-239">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-239">No</span></span>                              | <span data-ttu-id="73c66-240">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-240">No</span></span>                          | <span data-ttu-id="73c66-241">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-241">Yes</span></span>       |

<span data-ttu-id="73c66-242">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="73c66-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="73c66-243">Typowym sposobem użycia wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="73c66-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="73c66-244">Struktura zawiera również `TryAdd{LIFETIME}` metody rozszerzające, które rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="73c66-245">W poniższym przykładzie wywołanie `AddSingleton` rejestruje się `MyDependency` jako implementacja dla `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="73c66-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="73c66-246">Wywołanie nie `TryAddSingleton` działa, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="73c66-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="73c66-247">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="73c66-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="73c66-248">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="73c66-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="73c66-249">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="73c66-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="73c66-250">Podczas rejestrowania usług deweloper powinien dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="73c66-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="73c66-251">Zazwyczaj autorzy biblioteki używają, `TryAddEnumerable` Aby uniknąć rejestrowania wielu kopii implementacji w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="73c66-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="73c66-252">W poniższym przykładzie pierwsze wywołanie `TryAddEnumerable` rejestracji `MyDependency` jako implementacji dla `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="73c66-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="73c66-253">Drugie wywołanie rejestru `MyDependency` dla `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="73c66-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="73c66-254">Trzecie wywołanie nie działa `IMyDependency1` , ponieważ ma już zarejestrowana implementacja `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="73c66-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="73c66-255">Rejestracja usługi jest zazwyczaj kolejnością niezależną, z wyjątkiem rejestracji wielu implementacji tego samego typu.</span><span class="sxs-lookup"><span data-stu-id="73c66-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="73c66-256">`IServiceCollection` jest kolekcją <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> obiektów.</span><span class="sxs-lookup"><span data-stu-id="73c66-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="73c66-257">Poniższy przykład pokazuje, jak zarejestrować usługę przez utworzenie i dodanie `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="73c66-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="73c66-258">`Add{LIFETIME}`Metody wbudowane wykorzystują takie samo podejście.</span><span class="sxs-lookup"><span data-stu-id="73c66-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="73c66-259">Na przykład zobacz [kod źródłowy addscope](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="73c66-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="73c66-260">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="73c66-260">Constructor injection behavior</span></span>

<span data-ttu-id="73c66-261">Usługi można rozwiązać przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="73c66-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="73c66-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="73c66-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="73c66-263">Tworzy obiekty, które nie są zarejestrowane w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="73c66-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="73c66-264">Używany z funkcjami platformy, takimi jak [pomocnicy tagów](xref:mvc/views/tag-helpers/intro), kontrolery MVC i [powiązania modeli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="73c66-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="73c66-265">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="73c66-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="73c66-266">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="73c66-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="73c66-267">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="73c66-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="73c66-268">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="73c66-269">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="73c66-269">Entity Framework contexts</span></span>

<span data-ttu-id="73c66-270">Domyślnie kontekstu Entity Framework są dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle objęte zakresem żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="73c66-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="73c66-271">Aby użyć innego okresu istnienia, należy określić okres istnienia przy użyciu <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> przeciążenia.</span><span class="sxs-lookup"><span data-stu-id="73c66-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="73c66-272">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z okresem istnienia krótszym niż okres istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="73c66-273">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="73c66-273">Lifetime and registration options</span></span>

<span data-ttu-id="73c66-274">Aby zademonstrować różnicę między okresami istnienia usługi a ich opcjami rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadanie jako operację z identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="73c66-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="73c66-275">W zależności od sposobu skonfigurowania okresu istnienia usługi dla następujących interfejsów kontener zawiera te same lub inne wystąpienia usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="73c66-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="73c66-276">W poniższej `Operation` klasie zostały zaimplementowane wszystkie poprzednie interfejsy.</span><span class="sxs-lookup"><span data-stu-id="73c66-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="73c66-277">`Operation`Konstruktor generuje identyfikator GUID i przechowuje 4 ostatnie znaki we `OperationId` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="73c66-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="73c66-278">`Startup.ConfigureServices`Metoda tworzy wiele rejestracji `Operation` klasy zgodnie z nazwanymi okresami istnienia:</span><span class="sxs-lookup"><span data-stu-id="73c66-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="73c66-279">Przykładowa aplikacja pokazuje okresy istnienia obiektów zarówno w ramach żądań, jak i między nimi.</span><span class="sxs-lookup"><span data-stu-id="73c66-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="73c66-280">`IndexModel`I oprogramowanie pośredniczące żąda każdego rodzaju `IOperation` typu i rejestruje `OperationId` dla każdej z nich:</span><span class="sxs-lookup"><span data-stu-id="73c66-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="73c66-281">Podobnie jak w programie `IndexModel` , oprogramowanie pośredniczące rozwiązuje te same usługi:</span><span class="sxs-lookup"><span data-stu-id="73c66-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="73c66-282">Usługi w zakresie muszą zostać rozwiązane w `InvokeAsync` metodzie:</span><span class="sxs-lookup"><span data-stu-id="73c66-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="73c66-283">Dane wyjściowe rejestratora przedstawiają:</span><span class="sxs-lookup"><span data-stu-id="73c66-283">The logger output shows:</span></span>

* <span data-ttu-id="73c66-284">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="73c66-284">*Transient* objects are always different.</span></span> <span data-ttu-id="73c66-285">Wartość przejściowa `OperationId` różni się w `IndexModel` i w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="73c66-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="73c66-286">Obiekty o określonym *zakresie* są takie same dla każdego żądania, ale różnią się w zależności od każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="73c66-287">*Pojedyncze* obiekty są takie same dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="73c66-288">Aby zmniejszyć ilość danych wyjściowych rejestrowania, ustaw wartość opcji "Rejestrowanie: LogLevel: Microsoft: Error" w *appsettings.Development.js* w pliku:</span><span class="sxs-lookup"><span data-stu-id="73c66-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="73c66-289">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="73c66-289">Call services from main</span></span>

<span data-ttu-id="73c66-290">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="73c66-291">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="73c66-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="73c66-292">Poniższy przykład pokazuje, jak uzyskać dostęp do `IMyDependency` usługi w zakresie i wywołać jej `WriteMessage` metodę w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="73c66-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="73c66-293">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="73c66-293">Scope validation</span></span>

<span data-ttu-id="73c66-294">Gdy aplikacja jest uruchamiana w [środowisku deweloperskim](xref:fundamentals/environments) i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do skompilowania hosta, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="73c66-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="73c66-295">Usługi w zakresie nie są rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="73c66-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="73c66-296">Usługi w zakresie nie są wstawiane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="73c66-296">Scoped services aren't injected into singletons.</span></span>
* <span data-ttu-id="73c66-297">Usługi przejściowe nie są wstrzykiwane do pojedynczych lub usług objętych zakresem.</span><span class="sxs-lookup"><span data-stu-id="73c66-297">Transient services aren't injected into singletons or scoped services.</span></span>

<span data-ttu-id="73c66-298">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="73c66-298">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="73c66-299">Okres istnienia dostawcy usług głównych odpowiada okresowi istnienia aplikacji, gdy dostawca rozpoczyna pracę z aplikacją i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-299">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="73c66-300">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="73c66-300">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="73c66-301">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-301">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="73c66-302">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="73c66-302">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="73c66-303">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="73c66-303">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="73c66-304">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="73c66-304">Request Services</span></span>

<span data-ttu-id="73c66-305">Usługi dostępne w ramach żądania ASP.NET Core są udostępniane za pomocą kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="73c66-305">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="73c66-306">Gdy usługi są żądane w ramach żądania, usługi i ich zależności są rozwiązywane z `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="73c66-306">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="73c66-307">Struktura tworzy zakres na żądanie i `RequestServices` uwidacznia dostawcę usług objętych zakresem.</span><span class="sxs-lookup"><span data-stu-id="73c66-307">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="73c66-308">Wszystkie usługi objęte zakresem są prawidłowe przez cały czas, gdy żądanie jest aktywne.</span><span class="sxs-lookup"><span data-stu-id="73c66-308">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="73c66-309">Preferuj żądania zależności jako parametry konstruktora do rozpoznawania usług z `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="73c66-309">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="73c66-310">Dzięki temu klasy są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="73c66-310">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="73c66-311">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="73c66-311">Design services for dependency injection</span></span>

<span data-ttu-id="73c66-312">Podczas projektowania usług pod kątem iniekcji zależności:</span><span class="sxs-lookup"><span data-stu-id="73c66-312">When designing services for dependency injection:</span></span>

* <span data-ttu-id="73c66-313">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="73c66-313">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="73c66-314">Należy unikać tworzenia stanu globalnego przez projektowanie aplikacji do korzystania z usług pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="73c66-314">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="73c66-315">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-315">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="73c66-316">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-316">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="73c66-317">Zapewnianie niewielkich, dobrze przeprowadzonych i łatwych do przetestowania usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-317">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="73c66-318">Jeśli klasa ma dużo wstrzykiwanych zależności, może być znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="73c66-318">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="73c66-319">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowych klas.</span><span class="sxs-lookup"><span data-stu-id="73c66-319">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="73c66-320">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="73c66-320">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="73c66-321">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="73c66-321">Disposal of services</span></span>

<span data-ttu-id="73c66-322">Kontener wywołuje <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="73c66-322">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="73c66-323">Usługi rozpoznane przez kontener nigdy nie powinny zostać usunięte przez dewelopera.</span><span class="sxs-lookup"><span data-stu-id="73c66-323">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="73c66-324">Jeśli typ lub fabryka są zarejestrowane jako pojedyncze, kontener usuwa pojedyncze automatycznie.</span><span class="sxs-lookup"><span data-stu-id="73c66-324">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="73c66-325">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="73c66-325">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="73c66-326">Konsola debugowania pokazuje następujące dane wyjściowe po każdym odświeżeniu strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="73c66-326">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="73c66-327">Usługi, które nie zostały utworzone przez kontener usługi</span><span class="sxs-lookup"><span data-stu-id="73c66-327">Services not created by the service container</span></span>

<span data-ttu-id="73c66-328">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="73c66-328">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="73c66-329">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="73c66-329">In the preceding code:</span></span>

* <span data-ttu-id="73c66-330">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-330">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="73c66-331">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-331">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="73c66-332">Deweloper jest odpowiedzialny za likwidację usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-332">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="73c66-333">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="73c66-333">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="73c66-334">Przejściowy, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-334">Transient, limited lifetime</span></span>

<span data-ttu-id="73c66-335">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="73c66-335">**Scenario**</span></span>

<span data-ttu-id="73c66-336">Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="73c66-336">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="73c66-337">Wystąpienie jest rozwiązane w zakresie głównym (kontener główny).</span><span class="sxs-lookup"><span data-stu-id="73c66-337">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="73c66-338">Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.</span><span class="sxs-lookup"><span data-stu-id="73c66-338">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="73c66-339">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="73c66-339">**Solution**</span></span>

<span data-ttu-id="73c66-340">Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="73c66-340">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="73c66-341">W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego.</span><span class="sxs-lookup"><span data-stu-id="73c66-341">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="73c66-342">Jeśli typ końcowy ma inne zależności, fabryka może:</span><span class="sxs-lookup"><span data-stu-id="73c66-342">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="73c66-343">Odbierz <xref:System.IServiceProvider> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="73c66-343">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="73c66-344">Użyj polecenia, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-344">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="73c66-345">Wystąpienie udostępnione, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-345">Shared instance, limited lifetime</span></span>

<span data-ttu-id="73c66-346">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="73c66-346">**Scenario**</span></span>

<span data-ttu-id="73c66-347">Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> wystąpienie powinno mieć ograniczony okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="73c66-347">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="73c66-348">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="73c66-348">**Solution**</span></span>

<span data-ttu-id="73c66-349">Zarejestruj wystąpienie z okresem istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="73c66-349">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="73c66-350">Użyj <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> , aby utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="73c66-350">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="73c66-351">Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-351">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="73c66-352">Usuń zakres, gdy nie jest już wymagany.</span><span class="sxs-lookup"><span data-stu-id="73c66-352">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="73c66-353">Ogólne wytyczne interfejsu IDisposable</span><span class="sxs-lookup"><span data-stu-id="73c66-353">General IDisposable guidelines</span></span>

* <span data-ttu-id="73c66-354">Nie rejestruj <xref:System.IDisposable> wystąpień z przejściowym okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="73c66-354">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="73c66-355">Zamiast tego użyj wzorca fabryki.</span><span class="sxs-lookup"><span data-stu-id="73c66-355">Use the factory pattern instead.</span></span>
* <span data-ttu-id="73c66-356">Nie należy rozwiązywać <xref:System.IDisposable> wystąpień z okresem przejściowym lub z określonym zakresem w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="73c66-356">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="73c66-357">Jedynym wyjątkiem jest to, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> , ale nie jest idealnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="73c66-357">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="73c66-358">Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie.</span><span class="sxs-lookup"><span data-stu-id="73c66-358">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="73c66-359">Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-359">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="73c66-360">Używanie zakresów do kontrolowania okresów istnienia usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-360">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="73c66-361">Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.</span><span class="sxs-lookup"><span data-stu-id="73c66-361">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="73c66-362">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="73c66-362">Default service container replacement</span></span>

<span data-ttu-id="73c66-363">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="73c66-363">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="73c66-364">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest określona funkcja, która nie jest obsługiwana przez program, na przykład:</span><span class="sxs-lookup"><span data-stu-id="73c66-364">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="73c66-365">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="73c66-365">Property injection</span></span>
* <span data-ttu-id="73c66-366">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="73c66-366">Injection based on name</span></span>
* <span data-ttu-id="73c66-367">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="73c66-367">Child containers</span></span>
* <span data-ttu-id="73c66-368">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-368">Custom lifetime management</span></span>
* <span data-ttu-id="73c66-369">`Func<T>` Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="73c66-369">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="73c66-370">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="73c66-370">Convention-based registration</span></span>

<span data-ttu-id="73c66-371">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="73c66-371">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="73c66-372">Autofac</span><span class="sxs-lookup"><span data-stu-id="73c66-372">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="73c66-373">DryIoc</span><span class="sxs-lookup"><span data-stu-id="73c66-373">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="73c66-374">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="73c66-374">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="73c66-375">LightInject</span><span class="sxs-lookup"><span data-stu-id="73c66-375">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="73c66-376">Lamar</span><span class="sxs-lookup"><span data-stu-id="73c66-376">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="73c66-377">Stashbox</span><span class="sxs-lookup"><span data-stu-id="73c66-377">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="73c66-378">Unity</span><span class="sxs-lookup"><span data-stu-id="73c66-378">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="73c66-379">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="73c66-379">Thread safety</span></span>

<span data-ttu-id="73c66-380">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="73c66-380">Create thread-safe singleton services.</span></span> <span data-ttu-id="73c66-381">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątków w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="73c66-381">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="73c66-382">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="73c66-382">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="73c66-383">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie tylko raz przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="73c66-383">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="73c66-384">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="73c66-384">Recommendations</span></span>

* <span data-ttu-id="73c66-385">`async/await``Task`rozpoznawanie usług opartych na usłudze i nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="73c66-385">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="73c66-386">Ponieważ język C# nie obsługuje konstruktorów asynchronicznych, należy używać metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-386">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="73c66-387">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-387">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="73c66-388">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-388">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="73c66-389">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="73c66-389">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="73c66-390">Podobnie należy unikać obiektów "posiadacz danych", które istnieją tylko w celu zezwalania na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="73c66-390">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="73c66-391">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="73c66-391">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="73c66-392">Unikaj statycznego dostępu do usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-392">Avoid static access to services.</span></span> <span data-ttu-id="73c66-393">Na przykład Unikaj przechwytywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) jako statycznego pola lub właściwości do użycia w innym miejscu.</span><span class="sxs-lookup"><span data-stu-id="73c66-393">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="73c66-394">Utrzymuj szybkie i synchroniczne operacje.</span><span class="sxs-lookup"><span data-stu-id="73c66-394">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="73c66-395">Unikaj używania *wzorca lokalizatora usługi*.</span><span class="sxs-lookup"><span data-stu-id="73c66-395">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="73c66-396">Na przykład nie wywołuj, <xref:System.IServiceProvider.GetService%2A> Aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="73c66-396">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="73c66-397">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="73c66-397">**Incorrect:**</span></span>

    ![Niepoprawny kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="73c66-399">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="73c66-399">**Correct**:</span></span>

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
* <span data-ttu-id="73c66-400">Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="73c66-400">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="73c66-401">Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="73c66-401">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="73c66-402">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="73c66-402">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="73c66-403">Należy unikać wywołań do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73c66-403">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="73c66-404">Wywoływanie `BuildServiceProvider` zazwyczaj odbywa się, gdy deweloper chce rozwiązać usługę w programie `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73c66-404">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="73c66-405">Rozważmy na przykład przypadek, w którym `LoginPath` został załadowany z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="73c66-405">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="73c66-406">Należy unikać następujących metod:</span><span class="sxs-lookup"><span data-stu-id="73c66-406">Avoid the following approach:</span></span>

  ![zły kod wywołujący BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="73c66-408">Na powyższym obrazie, wybierając zieloną linię falistą w obszarze `services.BuildServiceProvider` pokazuje następujące ostrzeżenie ASP0000:</span><span class="sxs-lookup"><span data-stu-id="73c66-408">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="73c66-409">ASP0000 wywołanie "BuildServiceProvider" z kodu aplikacji spowoduje utworzenie dodatkowej kopii pojedynczych usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-409">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="73c66-410">Należy rozważyć alternatywy, takie jak "wstrzyknięcie usług" jako parametry do "configure".</span><span class="sxs-lookup"><span data-stu-id="73c66-410">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="73c66-411">Wywołanie `BuildServiceProvider` tworzy drugi kontener, który może tworzyć niezerwane pojedyncze i spowodować odwołania do grafów obiektów w wielu kontenerach.</span><span class="sxs-lookup"><span data-stu-id="73c66-411">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="73c66-412">Prawidłowym sposobem uzyskania `LoginPath` jest użycie wbudowanego wsparcia wzorca opcji dla di:</span><span class="sxs-lookup"><span data-stu-id="73c66-412">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="73c66-413">Nierozporządzalne usługi przejściowe są przechwytywane przez kontener do usuwania.</span><span class="sxs-lookup"><span data-stu-id="73c66-413">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="73c66-414">Może to obsłużyć przeciek pamięci, jeśli został rozwiązany z kontenera najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="73c66-414">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="73c66-415">Włącz weryfikację zakresu, aby upewnić się, że aplikacja nie ma usług objętych zakresem, które przechwytują pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="73c66-415">Enable scope validation to make sure the app doesn't have scoped services that capture singletons.</span></span> <span data-ttu-id="73c66-416">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="73c66-416">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="73c66-417">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="73c66-417">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="73c66-418">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="73c66-418">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="73c66-419">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="73c66-419">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="73c66-420">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="73c66-420">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="73c66-421">Zalecane wzorce dla wielu dzierżawców w programie DI</span><span class="sxs-lookup"><span data-stu-id="73c66-421">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="73c66-422">Element [sadu Core](https://github.com/OrchardCMS/OrchardCore) to struktura aplikacji służąca do tworzenia modularnych aplikacji wielodostępnych na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73c66-422">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="73c66-423">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją podstawową programu sadu](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="73c66-423">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="73c66-424">Zapoznaj się z [podstawowymi](https://github.com/OrchardCMS/OrchardCore.Samples) przykładami sadu, aby poznać przykłady tworzenia aplikacji modularnych i wielodostępnych przy użyciu tylko podstawowej platformy sadu bez żadnych funkcji specyficznych dla usługi CMS.</span><span class="sxs-lookup"><span data-stu-id="73c66-424">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="73c66-425">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="73c66-425">Framework-provided services</span></span>

<span data-ttu-id="73c66-426">`Startup.ConfigureServices`Metoda rejestruje usługi używane przez aplikację, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="73c66-426">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="73c66-427">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="73c66-427">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="73c66-428">W przypadku aplikacji opartych na szablonach ASP.NET Core struktura rejestruje więcej niż 250 usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-428">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="73c66-429">W poniższej tabeli przedstawiono niewielki przykład następujących usług zarejestrowanych w ramach platformy:</span><span class="sxs-lookup"><span data-stu-id="73c66-429">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="73c66-430">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="73c66-430">Service Type</span></span>                                                                                    | <span data-ttu-id="73c66-431">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-431">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="73c66-432">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-432">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="73c66-433">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="73c66-434">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="73c66-435">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="73c66-436">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-436">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="73c66-437">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="73c66-438">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-438">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="73c66-439">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="73c66-440">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="73c66-441">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="73c66-442">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-442">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="73c66-443">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="73c66-444">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="73c66-445">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-445">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="73c66-446">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="73c66-446">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="73c66-447">Wzorce konferencji NDC na potrzeby tworzenia aplikacji</span><span class="sxs-lookup"><span data-stu-id="73c66-447">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="73c66-448">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73c66-448">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="73c66-449">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="73c66-449">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="73c66-450">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="73c66-450">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="73c66-451">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="73c66-451">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="73c66-452">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="73c66-452">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73c66-453">[Steve Kowalski](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="73c66-453">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="73c66-454">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="73c66-454">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="73c66-455">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="73c66-455">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="73c66-456">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73c66-456">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="73c66-457">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="73c66-457">Overview of dependency injection</span></span>

<span data-ttu-id="73c66-458">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="73c66-458">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="73c66-459">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="73c66-459">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="73c66-460">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="73c66-460">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="73c66-461">`MyDependency`Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="73c66-461">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="73c66-462">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="73c66-462">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="73c66-463">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="73c66-463">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="73c66-464">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="73c66-464">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="73c66-465">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="73c66-465">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="73c66-466">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-466">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="73c66-467">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="73c66-467">This implementation is difficult to unit test.</span></span> <span data-ttu-id="73c66-468">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="73c66-468">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="73c66-469">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="73c66-469">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="73c66-470">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-470">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="73c66-471">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-471">Registration of the dependency in a service container.</span></span> <span data-ttu-id="73c66-472">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="73c66-472">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="73c66-473">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-473">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="73c66-474">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="73c66-474">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="73c66-475">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="73c66-475">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="73c66-476">W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="73c66-476">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="73c66-477">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="73c66-477">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="73c66-478">`MyDependency` żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="73c66-478">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="73c66-479">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="73c66-479">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="73c66-480">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-480">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="73c66-481">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-481">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="73c66-482">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="73c66-482">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="73c66-483">`IMyDependency` i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-483">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="73c66-484">`IMyDependency` jest zarejestrowany w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73c66-484">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="73c66-485">`ILogger<TCategoryName>` jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="73c66-485">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="73c66-486">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="73c66-486">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="73c66-487">W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="73c66-487">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="73c66-488">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-488">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="73c66-489">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="73c66-489">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="73c66-490">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="73c66-490">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="73c66-491">Na przykład `services.AddMvc()` dodaje Razor strony usług i wymagane przez MVC.</span><span class="sxs-lookup"><span data-stu-id="73c66-491">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="73c66-492">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="73c66-492">We recommended that apps follow this convention.</span></span> <span data-ttu-id="73c66-493">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-493">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="73c66-494">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string` ,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="73c66-494">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="73c66-495">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="73c66-495">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="73c66-496">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="73c66-496">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="73c66-497">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="73c66-497">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="73c66-498">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="73c66-498">Services injected into Startup</span></span>

<span data-ttu-id="73c66-499">Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="73c66-499">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="73c66-500">Usługi można wstrzyknąć do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="73c66-500">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="73c66-501">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="73c66-501">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="73c66-502">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="73c66-502">Framework-provided services</span></span>

<span data-ttu-id="73c66-503">`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="73c66-503">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="73c66-504">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="73c66-504">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="73c66-505">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="73c66-505">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="73c66-506">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="73c66-506">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="73c66-507">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="73c66-507">Service Type</span></span> | <span data-ttu-id="73c66-508">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-508">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="73c66-509">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-509">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="73c66-510">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="73c66-511">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="73c66-512">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="73c66-513">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-513">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="73c66-514">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="73c66-515">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-515">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="73c66-516">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="73c66-517">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="73c66-518">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="73c66-519">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-519">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="73c66-520">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="73c66-521">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="73c66-522">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-522">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="73c66-523">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="73c66-523">Register additional services with extension methods</span></span>

<span data-ttu-id="73c66-524">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać pojedynczej `Add{SERVICE_NAME}` metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="73c66-524">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="73c66-525">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="73c66-525">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="73c66-526">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="73c66-526">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="73c66-527">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="73c66-527">Service lifetimes</span></span>

<span data-ttu-id="73c66-528">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-528">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="73c66-529">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="73c66-529">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="73c66-530">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="73c66-530">Transient</span></span>

<span data-ttu-id="73c66-531">Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-531">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="73c66-532">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-532">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="73c66-533">W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-533">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="73c66-534">Zakresie</span><span class="sxs-lookup"><span data-stu-id="73c66-534">Scoped</span></span>

<span data-ttu-id="73c66-535">Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="73c66-535">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="73c66-536">W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="73c66-536">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="73c66-537">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` `InvokeAsync` metody lub.</span><span class="sxs-lookup"><span data-stu-id="73c66-537">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="73c66-538">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="73c66-538">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="73c66-539">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="73c66-539">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="73c66-540">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="73c66-540">Singleton</span></span>

<span data-ttu-id="73c66-541">Pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="73c66-541">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="73c66-542">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="73c66-542">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="73c66-543">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-543">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="73c66-544">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="73c66-544">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="73c66-545">W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-545">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="73c66-546">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="73c66-546">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="73c66-547">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="73c66-547">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="73c66-548">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="73c66-548">Service registration methods</span></span>

<span data-ttu-id="73c66-549">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="73c66-549">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="73c66-550">Metoda</span><span class="sxs-lookup"><span data-stu-id="73c66-550">Method</span></span> | <span data-ttu-id="73c66-551">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="73c66-551">Automatic</span></span><br><span data-ttu-id="73c66-552">object</span><span class="sxs-lookup"><span data-stu-id="73c66-552">object</span></span><br><span data-ttu-id="73c66-553">myśl</span><span class="sxs-lookup"><span data-stu-id="73c66-553">disposal</span></span> | <span data-ttu-id="73c66-554">Wiele</span><span class="sxs-lookup"><span data-stu-id="73c66-554">Multiple</span></span><br><span data-ttu-id="73c66-555">implementacje</span><span class="sxs-lookup"><span data-stu-id="73c66-555">implementations</span></span> | <span data-ttu-id="73c66-556">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="73c66-556">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="73c66-557">Przykład:</span><span class="sxs-lookup"><span data-stu-id="73c66-557">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="73c66-558">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-558">Yes</span></span> | <span data-ttu-id="73c66-559">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-559">Yes</span></span> | <span data-ttu-id="73c66-560">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-560">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="73c66-561">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="73c66-561">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="73c66-562">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-562">Yes</span></span> | <span data-ttu-id="73c66-563">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-563">Yes</span></span> | <span data-ttu-id="73c66-564">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-564">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="73c66-565">Przykład:</span><span class="sxs-lookup"><span data-stu-id="73c66-565">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="73c66-566">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-566">Yes</span></span> | <span data-ttu-id="73c66-567">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-567">No</span></span> | <span data-ttu-id="73c66-568">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-568">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="73c66-569">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="73c66-569">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="73c66-570">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-570">No</span></span> | <span data-ttu-id="73c66-571">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-571">Yes</span></span> | <span data-ttu-id="73c66-572">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-572">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="73c66-573">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="73c66-573">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="73c66-574">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-574">No</span></span> | <span data-ttu-id="73c66-575">Nie</span><span class="sxs-lookup"><span data-stu-id="73c66-575">No</span></span> | <span data-ttu-id="73c66-576">Tak</span><span class="sxs-lookup"><span data-stu-id="73c66-576">Yes</span></span> |

<span data-ttu-id="73c66-577">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="73c66-577">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="73c66-578">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="73c66-578">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="73c66-579">`TryAdd{LIFETIME}` Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-579">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="73c66-580">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="73c66-580">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="73c66-581">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="73c66-581">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="73c66-582">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="73c66-582">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="73c66-583">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="73c66-583">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="73c66-584">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="73c66-584">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="73c66-585">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="73c66-585">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="73c66-586">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="73c66-586">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="73c66-587">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="73c66-587">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="73c66-588">Drugi wiersz rejestruje `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="73c66-588">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="73c66-589">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="73c66-589">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="73c66-590">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="73c66-590">Constructor injection behavior</span></span>

<span data-ttu-id="73c66-591">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="73c66-591">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="73c66-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="73c66-593">`ActivatorUtilities` jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="73c66-593">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="73c66-594">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="73c66-594">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="73c66-595">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="73c66-595">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="73c66-596">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="73c66-596">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="73c66-597">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-597">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="73c66-598">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="73c66-598">Entity Framework contexts</span></span>

<span data-ttu-id="73c66-599">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="73c66-599">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="73c66-600">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="73c66-600">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="73c66-601">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="73c66-601">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="73c66-602">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="73c66-602">Lifetime and registration options</span></span>

<span data-ttu-id="73c66-603">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z unikatowym identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="73c66-603">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="73c66-604">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="73c66-604">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="73c66-605">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="73c66-605">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="73c66-606">`Operation`Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="73c66-606">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="73c66-607">`OperationService`Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="73c66-607">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="73c66-608">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="73c66-608">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="73c66-609">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` Usługa różni `IOperationTransient` się od `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="73c66-609">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="73c66-610">`OperationService` odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="73c66-610">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="73c66-611">Nowe wystąpienie daje inną wartość `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="73c66-611">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="73c66-612">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` Usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="73c66-612">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="73c66-613">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="73c66-613">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="73c66-614">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-614">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="73c66-615">W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="73c66-615">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="73c66-616">`IOperationSingletonInstance`Usługa używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="73c66-616">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="73c66-617">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="73c66-617">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="73c66-618">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="73c66-618">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="73c66-619">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="73c66-619">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="73c66-620">Następnie na stronie zostaną wyświetlone wszystkie wartości klasy modelu strony i usługi `OperationId` za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="73c66-620">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="73c66-621">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="73c66-621">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="73c66-622">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="73c66-622">**First request:**</span></span>

<span data-ttu-id="73c66-623">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="73c66-623">Controller operations:</span></span>

<span data-ttu-id="73c66-624">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="73c66-624">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="73c66-625">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="73c66-625">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="73c66-626">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="73c66-626">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="73c66-627">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="73c66-627">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="73c66-628">`OperationService` składowa</span><span class="sxs-lookup"><span data-stu-id="73c66-628">`OperationService` operations:</span></span>

<span data-ttu-id="73c66-629">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="73c66-629">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="73c66-630">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="73c66-630">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="73c66-631">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="73c66-631">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="73c66-632">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="73c66-632">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="73c66-633">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="73c66-633">**Second request:**</span></span>

<span data-ttu-id="73c66-634">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="73c66-634">Controller operations:</span></span>

<span data-ttu-id="73c66-635">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="73c66-635">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="73c66-636">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="73c66-636">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="73c66-637">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="73c66-637">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="73c66-638">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="73c66-638">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="73c66-639">`OperationService` składowa</span><span class="sxs-lookup"><span data-stu-id="73c66-639">`OperationService` operations:</span></span>

<span data-ttu-id="73c66-640">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="73c66-640">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="73c66-641">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="73c66-641">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="73c66-642">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="73c66-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="73c66-643">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="73c66-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="73c66-644">Zauważ, że `OperationId` wartości różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="73c66-644">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="73c66-645">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="73c66-645">*Transient* objects are always different.</span></span> <span data-ttu-id="73c66-646">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="73c66-646">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="73c66-647">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="73c66-647">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="73c66-648">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="73c66-648">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="73c66-649">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, czy `Operation` wystąpienie jest dostarczone w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73c66-649">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="73c66-650">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="73c66-650">Call services from main</span></span>

<span data-ttu-id="73c66-651">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-651">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="73c66-652">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="73c66-652">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="73c66-653">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="73c66-653">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="73c66-654">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="73c66-654">Scope validation</span></span>

<span data-ttu-id="73c66-655">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="73c66-655">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="73c66-656">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="73c66-656">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="73c66-657">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="73c66-657">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="73c66-658">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="73c66-658">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="73c66-659">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-659">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="73c66-660">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="73c66-660">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="73c66-661">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="73c66-661">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="73c66-662">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="73c66-662">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="73c66-663">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="73c66-663">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="73c66-664">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="73c66-664">Request Services</span></span>

<span data-ttu-id="73c66-665">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="73c66-665">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="73c66-666">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-666">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="73c66-667">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="73c66-667">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="73c66-668">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="73c66-668">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="73c66-669">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="73c66-669">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="73c66-670">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="73c66-670">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="73c66-671">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="73c66-671">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="73c66-672">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="73c66-672">Design services for dependency injection</span></span>

<span data-ttu-id="73c66-673">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="73c66-673">Best practices are to:</span></span>

* <span data-ttu-id="73c66-674">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-674">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="73c66-675">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="73c66-675">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="73c66-676">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="73c66-676">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="73c66-677">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-677">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="73c66-678">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-678">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="73c66-679">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="73c66-679">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="73c66-680">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="73c66-680">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="73c66-681">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="73c66-681">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="73c66-682">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="73c66-682">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="73c66-683">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="73c66-683">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="73c66-684">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="73c66-684">Disposal of services</span></span>

<span data-ttu-id="73c66-685">Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="73c66-685">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="73c66-686">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="73c66-686">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="73c66-687">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="73c66-687">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="73c66-688">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="73c66-688">In the following example:</span></span>

* <span data-ttu-id="73c66-689">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-689">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="73c66-690">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="73c66-690">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="73c66-691">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-691">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="73c66-692">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="73c66-692">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="73c66-693">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="73c66-693">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="73c66-694">Przejściowy, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-694">Transient, limited lifetime</span></span>

<span data-ttu-id="73c66-695">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="73c66-695">**Scenario**</span></span>

<span data-ttu-id="73c66-696">Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="73c66-696">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="73c66-697">Wystąpienie jest rozwiązane w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="73c66-697">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="73c66-698">Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.</span><span class="sxs-lookup"><span data-stu-id="73c66-698">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="73c66-699">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="73c66-699">**Solution**</span></span>

<span data-ttu-id="73c66-700">Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="73c66-700">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="73c66-701">W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego.</span><span class="sxs-lookup"><span data-stu-id="73c66-701">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="73c66-702">Jeśli typ końcowy ma inne zależności, fabryka może:</span><span class="sxs-lookup"><span data-stu-id="73c66-702">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="73c66-703">Odbierz <xref:System.IServiceProvider> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="73c66-703">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="73c66-704">Użyj, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-704">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="73c66-705">Wystąpienie udostępnione, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-705">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="73c66-706">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="73c66-706">**Scenario**</span></span>

<span data-ttu-id="73c66-707">Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> powinna mieć ograniczony okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="73c66-707">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="73c66-708">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="73c66-708">**Solution**</span></span>

<span data-ttu-id="73c66-709">Zarejestruj wystąpienie z okresem istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="73c66-709">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="73c66-710">Użyj, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> Aby rozpocząć i utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="73c66-710">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="73c66-711">Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-711">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="73c66-712">Usuń zakres, gdy okres istnienia powinien zostać zakończony.</span><span class="sxs-lookup"><span data-stu-id="73c66-712">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="73c66-713">Ogólne wskazówki</span><span class="sxs-lookup"><span data-stu-id="73c66-713">General Guidelines</span></span>

* <span data-ttu-id="73c66-714">Nie rejestruj <xref:System.IDisposable> wystąpień z zakresem przejściowym.</span><span class="sxs-lookup"><span data-stu-id="73c66-714">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="73c66-715">Zamiast tego użyj wzorca fabryki.</span><span class="sxs-lookup"><span data-stu-id="73c66-715">Use the factory pattern instead.</span></span>
* <span data-ttu-id="73c66-716">Nie należy rozwiązywać wystąpień przejściowych lub zakresów <xref:System.IDisposable> w zakresie w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="73c66-716">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="73c66-717">Jedyny ogólny wyjątek polega na tym, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> obiekt, który nie jest idealnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="73c66-717">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="73c66-718">Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie.</span><span class="sxs-lookup"><span data-stu-id="73c66-718">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="73c66-719">Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.</span><span class="sxs-lookup"><span data-stu-id="73c66-719">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="73c66-720">Zakresy powinny służyć do kontrolowania okresów istnienia usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-720">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="73c66-721">Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.</span><span class="sxs-lookup"><span data-stu-id="73c66-721">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="73c66-722">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="73c66-722">Default service container replacement</span></span>

<span data-ttu-id="73c66-723">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="73c66-723">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="73c66-724">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="73c66-724">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="73c66-725">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="73c66-725">Property injection</span></span>
* <span data-ttu-id="73c66-726">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="73c66-726">Injection based on name</span></span>
* <span data-ttu-id="73c66-727">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="73c66-727">Child containers</span></span>
* <span data-ttu-id="73c66-728">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="73c66-728">Custom lifetime management</span></span>
* <span data-ttu-id="73c66-729">`Func<T>` Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="73c66-729">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="73c66-730">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="73c66-730">Convention-based registration</span></span>

<span data-ttu-id="73c66-731">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="73c66-731">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="73c66-732">Autofac</span><span class="sxs-lookup"><span data-stu-id="73c66-732">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="73c66-733">DryIoc</span><span class="sxs-lookup"><span data-stu-id="73c66-733">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="73c66-734">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="73c66-734">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="73c66-735">LightInject</span><span class="sxs-lookup"><span data-stu-id="73c66-735">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="73c66-736">Lamar</span><span class="sxs-lookup"><span data-stu-id="73c66-736">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="73c66-737">Stashbox</span><span class="sxs-lookup"><span data-stu-id="73c66-737">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="73c66-738">Unity</span><span class="sxs-lookup"><span data-stu-id="73c66-738">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="73c66-739">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="73c66-739">Thread safety</span></span>

<span data-ttu-id="73c66-740">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="73c66-740">Create thread-safe singleton services.</span></span> <span data-ttu-id="73c66-741">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="73c66-741">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="73c66-742">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="73c66-742">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="73c66-743">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="73c66-743">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="73c66-744">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="73c66-744">Recommendations</span></span>

* <span data-ttu-id="73c66-745">`async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="73c66-745">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="73c66-746">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-746">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="73c66-747">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-747">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="73c66-748">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="73c66-748">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="73c66-749">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="73c66-749">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="73c66-750">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="73c66-750">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="73c66-751">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="73c66-751">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="73c66-752">Unikaj statycznego dostępu do usług.</span><span class="sxs-lookup"><span data-stu-id="73c66-752">Avoid static access to services.</span></span> <span data-ttu-id="73c66-753">Na przykład należy unikać statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu.</span><span class="sxs-lookup"><span data-stu-id="73c66-753">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="73c66-754">Unikaj używania *wzorca lokalizatora usługi*, który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="73c66-754">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="73c66-755">Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="73c66-755">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="73c66-756">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="73c66-756">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="73c66-757">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="73c66-757">**Correct**:</span></span>

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

* <span data-ttu-id="73c66-758">Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie wykonywania za pomocą polecenia <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="73c66-758">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="73c66-759">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="73c66-759">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="73c66-760">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="73c66-760">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="73c66-761">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="73c66-761">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="73c66-762">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="73c66-762">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="73c66-763">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="73c66-763">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73c66-764">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="73c66-764">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="73c66-765">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73c66-765">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="73c66-766">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="73c66-766">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="73c66-767">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="73c66-767">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="73c66-768">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="73c66-768">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="73c66-769">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="73c66-769">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
