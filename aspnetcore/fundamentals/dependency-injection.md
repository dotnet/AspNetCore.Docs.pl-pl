---
title: Wstrzykiwanie zależności w ASP.NET Core
author: rick-anderson
description: Dowiedz się, w jaki sposób ASP.NET Core implementuje iniekcję zależności i jak z niej korzystać.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 31db9aea9e0b7ed21cae2f87fbb9e2e649782697
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234468"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="58c5e-103">Wstrzykiwanie zależności w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58c5e-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58c5e-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="58c5e-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="58c5e-105">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="58c5e-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="58c5e-106">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="58c5e-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="58c5e-107">Aby uzyskać informacje na temat używania iniekcji zależności w aplikacjach innych niż aplikacje sieci Web, zobacz [iniekcja zależności w programie .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c5e-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="58c5e-108">Aby uzyskać więcej informacji na temat wstrzykiwania zależności opcji, zobacz <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="58c5e-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="58c5e-109">Ten temat zawiera informacje dotyczące iniekcji zależności w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58c5e-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="58c5e-110">Podstawowa dokumentacja dotycząca korzystania z iniekcji zależności jest zawarta w [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c5e-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="58c5e-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="58c5e-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="58c5e-112">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="58c5e-112">Overview of dependency injection</span></span>

<span data-ttu-id="58c5e-113">*Zależność* jest obiektem, od którego zależy inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="58c5e-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="58c5e-114">Zapoznaj się `MyDependency` z następującą klasą z `WriteMessage` metodą, od której zależą inne klasy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="58c5e-115">Klasa może utworzyć wystąpienie `MyDependency` klasy, aby użyć jej `WriteMessage` metody.</span><span class="sxs-lookup"><span data-stu-id="58c5e-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="58c5e-116">W poniższym przykładzie `MyDependency` Klasa jest zależna od `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="58c5e-117">Klasa tworzy i bezpośrednio zależy od `MyDependency` klasy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="58c5e-118">Zależności kodu, takie jak w poprzednim przykładzie, są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="58c5e-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="58c5e-119">Aby zastąpić `MyDependency` inną implementacją, `IndexModel` należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="58c5e-120">Jeśli `MyDependency` ma zależności, muszą one być również konfigurowane przez `IndexModel` klasę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="58c5e-121">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="58c5e-122">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="58c5e-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="58c5e-123">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="58c5e-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="58c5e-124">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="58c5e-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="58c5e-125">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="58c5e-126">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="58c5e-127">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="58c5e-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="58c5e-128">Usługi są zwykle zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="58c5e-129">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="58c5e-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="58c5e-130">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="58c5e-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="58c5e-131">W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje `WriteMessage` metodę:</span><span class="sxs-lookup"><span data-stu-id="58c5e-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="58c5e-132">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="58c5e-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="58c5e-133">Przykładowa aplikacja rejestruje `IMyDependency` usługę przy użyciu konkretnego typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="58c5e-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda rejestruje usługę z okresem istnienia w określonym zakresie, okresem istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="58c5e-135">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="58c5e-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="58c5e-136">W przykładowej aplikacji `IMyDependency` jest wymagana usługa, która jest używana do wywołania `WriteMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="58c5e-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="58c5e-137">Przy użyciu wzorca DI, kontroler:</span><span class="sxs-lookup"><span data-stu-id="58c5e-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="58c5e-138">Nie używa konkretnego typu `MyDependency` , tylko `IMyDependency` interfejsu, który implementuje.</span><span class="sxs-lookup"><span data-stu-id="58c5e-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="58c5e-139">Dzięki temu można łatwo zmienić implementację używaną przez kontroler bez modyfikowania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="58c5e-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="58c5e-140">Nie tworzy wystąpienia `MyDependency` , jest tworzone przez element di kontener.</span><span class="sxs-lookup"><span data-stu-id="58c5e-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="58c5e-141">Implementację `IMyDependency` interfejsu można ulepszyć za pomocą wbudowanego interfejsu API rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="58c5e-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="58c5e-142">Zaktualizowana `ConfigureServices` Metoda rejestruje nową `IMyDependency` implementację:</span><span class="sxs-lookup"><span data-stu-id="58c5e-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="58c5e-143">`MyDependency2` zależy od <xref:Microsoft.Extensions.Logging.ILogger%601> , który z nich żąda w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="58c5e-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="58c5e-144">`ILogger<TCategoryName>` to [Usługa udostępniona przez platformę](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="58c5e-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="58c5e-145">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="58c5e-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="58c5e-146">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="58c5e-147">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="58c5e-148">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności* , *wykresem zależności* lub *wykresem obiektów* .</span><span class="sxs-lookup"><span data-stu-id="58c5e-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="58c5e-149">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="58c5e-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="58c5e-150">W terminologii wtrysku zależności, usługa:</span><span class="sxs-lookup"><span data-stu-id="58c5e-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="58c5e-151">Jest zazwyczaj obiektem, który udostępnia usługę innym obiektom, takim jak `IMyDependency` Usługa.</span><span class="sxs-lookup"><span data-stu-id="58c5e-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="58c5e-152">Nie jest powiązany z usługą sieci Web, chociaż usługa może korzystać z usługi sieci Web.</span><span class="sxs-lookup"><span data-stu-id="58c5e-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="58c5e-153">Struktura zapewnia niezawodny system [rejestrowania](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="58c5e-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="58c5e-154">`IMyDependency`Implementacje opisane w powyższych przykładach zostały opracowane w celu zademonstrowania podstawowych di, a nie do zaimplementowania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="58c5e-155">Większość aplikacji nie musi być w trakcie pisania rejestratorów.</span><span class="sxs-lookup"><span data-stu-id="58c5e-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="58c5e-156">Poniższy kod ilustruje użycie domyślnego rejestrowania, które nie wymaga zarejestrowania żadnych usług w programie `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="58c5e-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="58c5e-157">Korzystając z powyższego kodu, nie ma potrzeby aktualizowania `ConfigureServices` , ponieważ [Rejestrowanie](xref:fundamentals/logging/index) jest dostarczane przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="58c5e-158">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="58c5e-158">Services injected into Startup</span></span>

<span data-ttu-id="58c5e-159">Usługi można wstrzyknąć do `Startup` konstruktora i `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="58c5e-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="58c5e-160">Tylko następujące usługi mogą być wstrzykiwane do konstruktora w `Startup` przypadku korzystania z hosta ogólnego ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="58c5e-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="58c5e-161">Wszystkie usługi zarejestrowane za pomocą DI Container można wstrzyknąć do `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="58c5e-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="58c5e-162">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup> i [Konfigurowanie dostępu podczas uruchamiania](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="58c5e-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="58c5e-163">Rejestrowanie grup usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="58c5e-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="58c5e-164">ASP.NET Core Framework używa konwencji do rejestracji grupy powiązanych usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="58c5e-165">Konwencja polega na użyciu pojedynczej `Add{GROUP_NAME}` metody rozszerzenia w celu zarejestrowania wszystkich usług wymaganych przez funkcję platformy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="58c5e-166">Na przykład <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. addcontrollers> Metoda rozszerzenia rejestruje usługi wymagane przez kontrolery MVC.</span><span class="sxs-lookup"><span data-stu-id="58c5e-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="58c5e-167">Poniższy kod jest generowany przez Razor szablon stron przy użyciu poszczególnych kont użytkowników i pokazuje, jak dodać dodatkowe usługi do kontenera przy użyciu metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="58c5e-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="58c5e-168">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="58c5e-168">Service lifetimes</span></span>

<span data-ttu-id="58c5e-169">Zobacz [okresy istnienia usługi](/dotnet/core/extensions/dependency-injection#service-lifetimes) podczas [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="58c5e-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="58c5e-170">Aby korzystać z usług objętych zakresem w oprogramowaniu pośredniczącym, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="58c5e-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="58c5e-171">Wsuń usługę do programu pośredniczącego `Invoke` lub `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="58c5e-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="58c5e-172">Użycie [iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) zgłasza wyjątek czasu wykonywania, ponieważ wymusza, aby usługa w zakresie działała jak pojedyncza.</span><span class="sxs-lookup"><span data-stu-id="58c5e-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="58c5e-173">Przykład w sekcji [Opcje czasu istnienia i rejestracji](#lifetime-and-registration-options) przedstawia `InvokeAsync` podejście.</span><span class="sxs-lookup"><span data-stu-id="58c5e-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="58c5e-174">Używaj [oprogramowania pośredniczącego opartego na fabryce](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="58c5e-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="58c5e-175">Oprogramowanie pośredniczące zarejestrowane przy użyciu tego podejścia jest aktywowane na żądanie klienta (połączenie), które umożliwia wprowadzanie w zakresie usług objętych zakresem do metody oprogramowania pośredniczącego `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="58c5e-176">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="58c5e-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="58c5e-177">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="58c5e-177">Service registration methods</span></span>

<span data-ttu-id="58c5e-178">Zobacz [metody rejestracji usług](/dotnet/core/extensions/dependency-injection#service-registration-methods) w [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="58c5e-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="58c5e-179">Typowym sposobem użycia wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="58c5e-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="58c5e-180">Zarejestrowanie usługi z użyciem tylko typu implementacji jest równoznaczne z zarejestrowaniem tej usługi z tą samą implementacją i typem usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="58c5e-181">Dlatego nie można zarejestrować wielu implementacji usługi przy użyciu metod, które nie pobierają jawnego typu usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="58c5e-182">Metody te mogą rejestrować wiele *wystąpień* usługi, ale wszystkie będą miały ten sam typ *implementacji* .</span><span class="sxs-lookup"><span data-stu-id="58c5e-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="58c5e-183">Wszystkie powyższe metody rejestracji usług mogą służyć do rejestrowania wielu wystąpień usług tego samego typu usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="58c5e-184">W poniższym przykładzie `AddSingleton` jest wywoływana dwukrotnie `IMyDependency` jako typ usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="58c5e-185">Drugie wywołanie `AddSingleton` przesłania poprzednią, gdy zostanie rozpoznane jako `IMyDependency` i dodaje do poprzedniego, gdy wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="58c5e-186">Usługi są wyświetlane w kolejności, w jakiej zostały zarejestrowane po rozwiązaniu przez `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="58c5e-187">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="58c5e-187">Constructor injection behavior</span></span>

<span data-ttu-id="58c5e-188">Zobacz [zachowanie iniekcji konstruktora](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) przy [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="58c5e-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="58c5e-189">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="58c5e-189">Entity Framework contexts</span></span>

<span data-ttu-id="58c5e-190">Domyślnie kontekstu Entity Framework są dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle objęte zakresem żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="58c5e-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="58c5e-191">Aby użyć innego okresu istnienia, należy określić okres istnienia przy użyciu <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> przeciążenia.</span><span class="sxs-lookup"><span data-stu-id="58c5e-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="58c5e-192">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z okresem istnienia krótszym niż okres istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="58c5e-193">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="58c5e-193">Lifetime and registration options</span></span>

<span data-ttu-id="58c5e-194">Aby zademonstrować różnicę między okresami istnienia usługi a ich opcjami rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadanie jako operację z identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="58c5e-195">W zależności od sposobu skonfigurowania okresu istnienia usługi dla następujących interfejsów kontener zawiera te same lub inne wystąpienia usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="58c5e-196">W poniższej `Operation` klasie zostały zaimplementowane wszystkie poprzednie interfejsy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="58c5e-197">`Operation`Konstruktor generuje identyfikator GUID i przechowuje 4 ostatnie znaki we `OperationId` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="58c5e-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="58c5e-198">`Startup.ConfigureServices`Metoda tworzy wiele rejestracji `Operation` klasy zgodnie z nazwanymi okresami istnienia:</span><span class="sxs-lookup"><span data-stu-id="58c5e-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="58c5e-199">Przykładowa aplikacja pokazuje okresy istnienia obiektów zarówno w ramach żądań, jak i między nimi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="58c5e-200">`IndexModel`I oprogramowanie pośredniczące żąda każdego rodzaju `IOperation` typu i rejestruje `OperationId` dla każdej z nich:</span><span class="sxs-lookup"><span data-stu-id="58c5e-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="58c5e-201">Podobnie jak w programie `IndexModel` , oprogramowanie pośredniczące rozwiązuje te same usługi:</span><span class="sxs-lookup"><span data-stu-id="58c5e-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="58c5e-202">Usługi w zakresie muszą zostać rozwiązane w `InvokeAsync` metodzie:</span><span class="sxs-lookup"><span data-stu-id="58c5e-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="58c5e-203">Dane wyjściowe rejestratora przedstawiają:</span><span class="sxs-lookup"><span data-stu-id="58c5e-203">The logger output shows:</span></span>

* <span data-ttu-id="58c5e-204">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="58c5e-204">*Transient* objects are always different.</span></span> <span data-ttu-id="58c5e-205">Wartość przejściowa `OperationId` różni się w `IndexModel` i w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="58c5e-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="58c5e-206">Obiekty o określonym *zakresie* są takie same dla każdego żądania, ale różnią się w zależności od każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="58c5e-207">*Pojedyncze* obiekty są takie same dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="58c5e-208">Aby zmniejszyć ilość danych wyjściowych rejestrowania, ustaw wartość opcji "Rejestrowanie: LogLevel: Microsoft: Error" w *appsettings.Development.js* w pliku:</span><span class="sxs-lookup"><span data-stu-id="58c5e-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="58c5e-209">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="58c5e-209">Call services from main</span></span>

<span data-ttu-id="58c5e-210">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="58c5e-211">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="58c5e-212">Poniższy przykład pokazuje, jak uzyskać dostęp do `IMyDependency` usługi w zakresie i wywołać jej `WriteMessage` metodę w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="58c5e-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="58c5e-213">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="58c5e-213">Scope validation</span></span>

<span data-ttu-id="58c5e-214">Zobacz [zachowanie iniekcji konstruktora](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) przy [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="58c5e-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="58c5e-215">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="58c5e-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="58c5e-216">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="58c5e-216">Request Services</span></span>

<span data-ttu-id="58c5e-217">Usługi dostępne w ramach żądania ASP.NET Core są udostępniane za pomocą kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="58c5e-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="58c5e-218">Gdy usługi są żądane w ramach żądania, usługi i ich zależności są rozwiązywane z `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="58c5e-219">Struktura tworzy zakres na żądanie i `RequestServices` uwidacznia dostawcę usług objętych zakresem.</span><span class="sxs-lookup"><span data-stu-id="58c5e-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="58c5e-220">Wszystkie usługi objęte zakresem są prawidłowe przez cały czas, gdy żądanie jest aktywne.</span><span class="sxs-lookup"><span data-stu-id="58c5e-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="58c5e-221">Preferuj żądania zależności jako parametry konstruktora do rozpoznawania usług z `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="58c5e-222">Dzięki temu klasy są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="58c5e-223">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="58c5e-223">Design services for dependency injection</span></span>

<span data-ttu-id="58c5e-224">Podczas projektowania usług pod kątem iniekcji zależności:</span><span class="sxs-lookup"><span data-stu-id="58c5e-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="58c5e-225">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="58c5e-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="58c5e-226">Należy unikać tworzenia stanu globalnego przez projektowanie aplikacji do korzystania z usług pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="58c5e-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="58c5e-227">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="58c5e-228">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="58c5e-229">Zapewnianie niewielkich, dobrze przeprowadzonych i łatwych do przetestowania usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="58c5e-230">Jeśli klasa ma dużo wstrzykiwanych zależności, może być znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="58c5e-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="58c5e-231">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowych klas.</span><span class="sxs-lookup"><span data-stu-id="58c5e-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="58c5e-232">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="58c5e-232">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="58c5e-233">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="58c5e-233">Disposal of services</span></span>

<span data-ttu-id="58c5e-234">Kontener wywołuje <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="58c5e-235">Usługi rozpoznane przez kontener nigdy nie powinny zostać usunięte przez dewelopera.</span><span class="sxs-lookup"><span data-stu-id="58c5e-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="58c5e-236">Jeśli typ lub fabryka są zarejestrowane jako pojedyncze, kontener usuwa pojedyncze automatycznie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="58c5e-237">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="58c5e-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="58c5e-238">Konsola debugowania pokazuje następujące dane wyjściowe po każdym odświeżeniu strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="58c5e-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="58c5e-239">Usługi, które nie zostały utworzone przez kontener usługi</span><span class="sxs-lookup"><span data-stu-id="58c5e-239">Services not created by the service container</span></span>

<span data-ttu-id="58c5e-240">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="58c5e-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="58c5e-241">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="58c5e-241">In the preceding code:</span></span>

* <span data-ttu-id="58c5e-242">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="58c5e-243">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="58c5e-244">Deweloper jest odpowiedzialny za likwidację usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="58c5e-245">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="58c5e-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="58c5e-246">Zobacz [wskazówki interfejsu IDisposable dla wystąpienia przejściowego i udostępnionego](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) w [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="58c5e-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="58c5e-247">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="58c5e-247">Default service container replacement</span></span>

<span data-ttu-id="58c5e-248">Zobacz [domyślne zastępowanie kontenera usług](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) podczas [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="58c5e-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="58c5e-249">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="58c5e-249">Recommendations</span></span>

<span data-ttu-id="58c5e-250">Zobacz [zalecenia dotyczące](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) [iniekcji zależności w programie .NET](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="58c5e-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="58c5e-251">Unikaj używania *wzorca lokalizatora usługi* .</span><span class="sxs-lookup"><span data-stu-id="58c5e-251">Avoid using the *service locator pattern* .</span></span> <span data-ttu-id="58c5e-252">Na przykład nie wywołuj, <xref:System.IServiceProvider.GetService%2A> Aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="58c5e-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="58c5e-253">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="58c5e-253">**Incorrect:**</span></span>

    ![Niepoprawny kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="58c5e-255">**Poprawne** :</span><span class="sxs-lookup"><span data-stu-id="58c5e-255">**Correct** :</span></span>

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
* <span data-ttu-id="58c5e-256">Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="58c5e-257">Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="58c5e-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="58c5e-258">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="58c5e-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="58c5e-259">Należy unikać wywołań do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="58c5e-260">Wywoływanie `BuildServiceProvider` zazwyczaj odbywa się, gdy deweloper chce rozwiązać usługę w programie `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="58c5e-261">Rozważmy na przykład przypadek, w którym `LoginPath` został załadowany z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="58c5e-262">Należy unikać następujących metod:</span><span class="sxs-lookup"><span data-stu-id="58c5e-262">Avoid the following approach:</span></span>

  ![zły kod wywołujący BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="58c5e-264">Na powyższym obrazie, wybierając zieloną linię falistą w obszarze `services.BuildServiceProvider` pokazuje następujące ostrzeżenie ASP0000:</span><span class="sxs-lookup"><span data-stu-id="58c5e-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="58c5e-265">ASP0000 wywołanie "BuildServiceProvider" z kodu aplikacji spowoduje utworzenie dodatkowej kopii pojedynczych usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="58c5e-266">Należy rozważyć alternatywy, takie jak "wstrzyknięcie usług" jako parametry do "configure".</span><span class="sxs-lookup"><span data-stu-id="58c5e-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="58c5e-267">Wywołanie `BuildServiceProvider` tworzy drugi kontener, który może tworzyć niezerwane pojedyncze i spowodować odwołania do grafów obiektów w wielu kontenerach.</span><span class="sxs-lookup"><span data-stu-id="58c5e-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="58c5e-268">Prawidłowym sposobem uzyskania `LoginPath` jest użycie wbudowanego wsparcia wzorca opcji dla di:</span><span class="sxs-lookup"><span data-stu-id="58c5e-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="58c5e-269">Nierozporządzalne usługi przejściowe są przechwytywane przez kontener do usuwania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="58c5e-270">Może to obsłużyć przeciek pamięci, jeśli został rozwiązany z kontenera najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="58c5e-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="58c5e-271">Włącz weryfikację zakresu, aby upewnić się, że aplikacja nie ma pojedynczych, które przechwytują usługi o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="58c5e-272">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="58c5e-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="58c5e-273">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="58c5e-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="58c5e-274">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="58c5e-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="58c5e-275">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="58c5e-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="58c5e-276">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="58c5e-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="58c5e-277">Zalecane wzorce dla wielu dzierżawców w programie DI</span><span class="sxs-lookup"><span data-stu-id="58c5e-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="58c5e-278">Element [sadu Core](https://github.com/OrchardCMS/OrchardCore) to struktura aplikacji służąca do tworzenia modularnych aplikacji wielodostępnych na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58c5e-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="58c5e-279">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją podstawową programu sadu](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="58c5e-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="58c5e-280">Zapoznaj się z [podstawowymi](https://github.com/OrchardCMS/OrchardCore.Samples) przykładami sadu, aby poznać przykłady tworzenia aplikacji modularnych i wielodostępnych przy użyciu tylko podstawowej platformy sadu bez żadnych funkcji specyficznych dla usługi CMS.</span><span class="sxs-lookup"><span data-stu-id="58c5e-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="58c5e-281">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="58c5e-281">Framework-provided services</span></span>

<span data-ttu-id="58c5e-282">`Startup.ConfigureServices`Metoda rejestruje usługi używane przez aplikację, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="58c5e-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="58c5e-283">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="58c5e-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="58c5e-284">W przypadku aplikacji opartych na szablonach ASP.NET Core struktura rejestruje więcej niż 250 usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="58c5e-285">W poniższej tabeli przedstawiono niewielki przykład następujących usług zarejestrowanych w ramach platformy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="58c5e-286">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="58c5e-286">Service Type</span></span>                                                                                    | <span data-ttu-id="58c5e-287">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="58c5e-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="58c5e-288">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="58c5e-289">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="58c5e-290">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="58c5e-291">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="58c5e-292">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="58c5e-293">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="58c5e-294">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="58c5e-295">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="58c5e-296">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="58c5e-297">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="58c5e-298">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="58c5e-299">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="58c5e-300">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="58c5e-301">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="58c5e-302">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="58c5e-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="58c5e-303">Wzorce konferencji NDC na potrzeby tworzenia aplikacji</span><span class="sxs-lookup"><span data-stu-id="58c5e-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="58c5e-304">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58c5e-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="58c5e-305">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="58c5e-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="58c5e-306">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="58c5e-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="58c5e-307">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="58c5e-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="58c5e-308">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="58c5e-308">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58c5e-309">[Steve Kowalski](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="58c5e-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="58c5e-310">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="58c5e-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="58c5e-311">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="58c5e-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="58c5e-312">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="58c5e-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="58c5e-313">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="58c5e-313">Overview of dependency injection</span></span>

<span data-ttu-id="58c5e-314">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="58c5e-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="58c5e-315">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="58c5e-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="58c5e-316">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="58c5e-317">`MyDependency`Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="58c5e-318">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="58c5e-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="58c5e-319">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="58c5e-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="58c5e-320">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="58c5e-321">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="58c5e-322">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="58c5e-323">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="58c5e-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="58c5e-324">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="58c5e-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="58c5e-325">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="58c5e-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="58c5e-326">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="58c5e-327">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="58c5e-328">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="58c5e-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="58c5e-329">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="58c5e-330">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="58c5e-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="58c5e-331">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="58c5e-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="58c5e-332">W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="58c5e-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="58c5e-333">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="58c5e-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="58c5e-334">`MyDependency` żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="58c5e-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="58c5e-335">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="58c5e-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="58c5e-336">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="58c5e-337">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="58c5e-338">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności* , *wykresem zależności* lub *wykresem obiektów* .</span><span class="sxs-lookup"><span data-stu-id="58c5e-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph* .</span></span>

<span data-ttu-id="58c5e-339">`IMyDependency` i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="58c5e-340">`IMyDependency` jest zarejestrowany w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="58c5e-341">`ILogger<TCategoryName>` jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="58c5e-342">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="58c5e-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="58c5e-343">W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="58c5e-344">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="58c5e-345">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="58c5e-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="58c5e-346">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="58c5e-346">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="58c5e-347">Na przykład `services.AddMvc()` dodaje Razor strony usług i wymagane przez MVC.</span><span class="sxs-lookup"><span data-stu-id="58c5e-347">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="58c5e-348">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="58c5e-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="58c5e-349">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-349">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="58c5e-350">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string` ,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="58c5e-350">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="58c5e-351">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="58c5e-351">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="58c5e-352">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-352">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="58c5e-353">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="58c5e-353">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="58c5e-354">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="58c5e-354">Services injected into Startup</span></span>

<span data-ttu-id="58c5e-355">Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="58c5e-355">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="58c5e-356">Usługi można wstrzyknąć do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="58c5e-356">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="58c5e-357">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="58c5e-357">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="58c5e-358">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="58c5e-358">Framework-provided services</span></span>

<span data-ttu-id="58c5e-359">`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="58c5e-359">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="58c5e-360">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="58c5e-360">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="58c5e-361">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-361">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="58c5e-362">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-362">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="58c5e-363">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="58c5e-363">Service Type</span></span> | <span data-ttu-id="58c5e-364">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="58c5e-364">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="58c5e-365">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-365">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="58c5e-366">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-366">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="58c5e-367">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-367">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="58c5e-368">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-368">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="58c5e-369">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-369">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="58c5e-370">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="58c5e-371">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-371">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="58c5e-372">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-372">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="58c5e-373">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-373">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="58c5e-374">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-374">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="58c5e-375">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-375">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="58c5e-376">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-376">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="58c5e-377">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-377">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="58c5e-378">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-378">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="58c5e-379">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="58c5e-379">Register additional services with extension methods</span></span>

<span data-ttu-id="58c5e-380">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać pojedynczej `Add{SERVICE_NAME}` metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-380">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="58c5e-381">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="58c5e-381">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="58c5e-382">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="58c5e-382">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="58c5e-383">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="58c5e-383">Service lifetimes</span></span>

<span data-ttu-id="58c5e-384">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-384">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="58c5e-385">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="58c5e-385">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="58c5e-386">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="58c5e-386">Transient</span></span>

<span data-ttu-id="58c5e-387">Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-387">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="58c5e-388">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-388">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="58c5e-389">W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-389">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="58c5e-390">Zakresie</span><span class="sxs-lookup"><span data-stu-id="58c5e-390">Scoped</span></span>

<span data-ttu-id="58c5e-391">Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="58c5e-391">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="58c5e-392">W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-392">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="58c5e-393">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` `InvokeAsync` metody lub.</span><span class="sxs-lookup"><span data-stu-id="58c5e-393">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="58c5e-394">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="58c5e-394">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="58c5e-395">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="58c5e-395">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="58c5e-396">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="58c5e-396">Singleton</span></span>

<span data-ttu-id="58c5e-397">Pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="58c5e-397">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="58c5e-398">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="58c5e-398">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="58c5e-399">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-399">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="58c5e-400">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-400">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="58c5e-401">W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-401">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="58c5e-402">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="58c5e-402">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="58c5e-403">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="58c5e-403">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="58c5e-404">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="58c5e-404">Service registration methods</span></span>

<span data-ttu-id="58c5e-405">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="58c5e-405">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="58c5e-406">Metoda</span><span class="sxs-lookup"><span data-stu-id="58c5e-406">Method</span></span> | <span data-ttu-id="58c5e-407">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="58c5e-407">Automatic</span></span><br><span data-ttu-id="58c5e-408">object</span><span class="sxs-lookup"><span data-stu-id="58c5e-408">object</span></span><br><span data-ttu-id="58c5e-409">myśl</span><span class="sxs-lookup"><span data-stu-id="58c5e-409">disposal</span></span> | <span data-ttu-id="58c5e-410">Wiele</span><span class="sxs-lookup"><span data-stu-id="58c5e-410">Multiple</span></span><br><span data-ttu-id="58c5e-411">implementacje</span><span class="sxs-lookup"><span data-stu-id="58c5e-411">implementations</span></span> | <span data-ttu-id="58c5e-412">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="58c5e-412">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="58c5e-413">Przykład:</span><span class="sxs-lookup"><span data-stu-id="58c5e-413">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="58c5e-414">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-414">Yes</span></span> | <span data-ttu-id="58c5e-415">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-415">Yes</span></span> | <span data-ttu-id="58c5e-416">Nie</span><span class="sxs-lookup"><span data-stu-id="58c5e-416">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="58c5e-417">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="58c5e-417">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="58c5e-418">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-418">Yes</span></span> | <span data-ttu-id="58c5e-419">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-419">Yes</span></span> | <span data-ttu-id="58c5e-420">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-420">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="58c5e-421">Przykład:</span><span class="sxs-lookup"><span data-stu-id="58c5e-421">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="58c5e-422">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-422">Yes</span></span> | <span data-ttu-id="58c5e-423">Nie</span><span class="sxs-lookup"><span data-stu-id="58c5e-423">No</span></span> | <span data-ttu-id="58c5e-424">Nie</span><span class="sxs-lookup"><span data-stu-id="58c5e-424">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="58c5e-425">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="58c5e-425">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="58c5e-426">Nie</span><span class="sxs-lookup"><span data-stu-id="58c5e-426">No</span></span> | <span data-ttu-id="58c5e-427">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-427">Yes</span></span> | <span data-ttu-id="58c5e-428">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-428">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="58c5e-429">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="58c5e-429">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="58c5e-430">Nie</span><span class="sxs-lookup"><span data-stu-id="58c5e-430">No</span></span> | <span data-ttu-id="58c5e-431">Nie</span><span class="sxs-lookup"><span data-stu-id="58c5e-431">No</span></span> | <span data-ttu-id="58c5e-432">Tak</span><span class="sxs-lookup"><span data-stu-id="58c5e-432">Yes</span></span> |

<span data-ttu-id="58c5e-433">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="58c5e-433">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="58c5e-434">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="58c5e-434">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="58c5e-435">Zarejestrowanie usługi z użyciem tylko typu implementacji jest równoznaczne z zarejestrowaniem tej usługi z tą samą implementacją i typem usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-435">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="58c5e-436">Dlatego nie można zarejestrować wielu implementacji usługi przy użyciu metod, które nie pobierają jawnego typu usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-436">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="58c5e-437">Metody te mogą rejestrować wiele *wystąpień* usługi, ale wszystkie będą miały ten sam typ *implementacji* .</span><span class="sxs-lookup"><span data-stu-id="58c5e-437">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="58c5e-438">Wszystkie powyższe metody rejestracji usług mogą służyć do rejestrowania wielu wystąpień usług tego samego typu usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-438">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="58c5e-439">W poniższym przykładzie `AddSingleton` jest wywoływana dwukrotnie `IMyDependency` jako typ usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-439">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="58c5e-440">Drugie wywołanie `AddSingleton` przesłania poprzednią, gdy zostanie rozpoznane jako `IMyDependency` i dodaje do poprzedniego, gdy wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<IMyDependency>` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-440">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="58c5e-441">Usługi są wyświetlane w kolejności, w jakiej zostały zarejestrowane po rozwiązaniu przez `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-441">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="58c5e-442">Struktura zawiera również `TryAdd{LIFETIME}` metody rozszerzające, które rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-442">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="58c5e-443">W poniższym przykładzie wywołanie `AddSingleton` rejestruje się `MyDependency` jako implementacja dla `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-443">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="58c5e-444">Wywołanie nie `TryAddSingleton` działa, ponieważ `IMyDependency` ma już zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="58c5e-444">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="58c5e-445">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="58c5e-445">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="58c5e-446">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu* .</span><span class="sxs-lookup"><span data-stu-id="58c5e-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type* .</span></span> <span data-ttu-id="58c5e-447">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-447">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="58c5e-448">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="58c5e-448">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="58c5e-449">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="58c5e-449">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="58c5e-450">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-450">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="58c5e-451">Drugi wiersz rejestruje `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-451">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="58c5e-452">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="58c5e-452">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="58c5e-453">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="58c5e-453">Constructor injection behavior</span></span>

<span data-ttu-id="58c5e-454">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-454">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="58c5e-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="58c5e-456">`ActivatorUtilities` jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="58c5e-456">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="58c5e-457">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="58c5e-457">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="58c5e-458">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="58c5e-458">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="58c5e-459">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="58c5e-459">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="58c5e-460">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-460">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="58c5e-461">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="58c5e-461">Entity Framework contexts</span></span>

<span data-ttu-id="58c5e-462">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="58c5e-462">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="58c5e-463">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="58c5e-463">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="58c5e-464">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="58c5e-464">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="58c5e-465">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="58c5e-465">Lifetime and registration options</span></span>

<span data-ttu-id="58c5e-466">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z unikatowym identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-466">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="58c5e-467">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-467">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="58c5e-468">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-468">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="58c5e-469">`Operation`Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="58c5e-469">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="58c5e-470">`OperationService`Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="58c5e-470">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="58c5e-471">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="58c5e-471">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="58c5e-472">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` Usługa różni `IOperationTransient` się od `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-472">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="58c5e-473">`OperationService` odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-473">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="58c5e-474">Nowe wystąpienie daje inną wartość `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-474">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="58c5e-475">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` Usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="58c5e-475">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="58c5e-476">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="58c5e-476">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="58c5e-477">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-477">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="58c5e-478">W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="58c5e-478">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="58c5e-479">`IOperationSingletonInstance`Usługa używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-479">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="58c5e-480">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="58c5e-480">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="58c5e-481">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-481">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="58c5e-482">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-482">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="58c5e-483">Następnie na stronie zostaną wyświetlone wszystkie wartości klasy modelu strony i usługi `OperationId` za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="58c5e-483">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="58c5e-484">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="58c5e-484">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="58c5e-485">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="58c5e-485">**First request:**</span></span>

<span data-ttu-id="58c5e-486">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="58c5e-486">Controller operations:</span></span>

<span data-ttu-id="58c5e-487">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="58c5e-487">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="58c5e-488">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="58c5e-488">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="58c5e-489">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="58c5e-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="58c5e-490">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="58c5e-490">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="58c5e-491">`OperationService` składowa</span><span class="sxs-lookup"><span data-stu-id="58c5e-491">`OperationService` operations:</span></span>

<span data-ttu-id="58c5e-492">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="58c5e-492">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="58c5e-493">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="58c5e-493">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="58c5e-494">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="58c5e-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="58c5e-495">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="58c5e-495">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="58c5e-496">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="58c5e-496">**Second request:**</span></span>

<span data-ttu-id="58c5e-497">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="58c5e-497">Controller operations:</span></span>

<span data-ttu-id="58c5e-498">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="58c5e-498">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="58c5e-499">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="58c5e-499">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="58c5e-500">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="58c5e-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="58c5e-501">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="58c5e-501">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="58c5e-502">`OperationService` składowa</span><span class="sxs-lookup"><span data-stu-id="58c5e-502">`OperationService` operations:</span></span>

<span data-ttu-id="58c5e-503">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="58c5e-503">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="58c5e-504">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="58c5e-504">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="58c5e-505">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="58c5e-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="58c5e-506">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="58c5e-506">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="58c5e-507">Zauważ, że `OperationId` wartości różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="58c5e-507">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="58c5e-508">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="58c5e-508">*Transient* objects are always different.</span></span> <span data-ttu-id="58c5e-509">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="58c5e-509">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="58c5e-510">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="58c5e-510">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="58c5e-511">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="58c5e-511">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="58c5e-512">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, czy `Operation` wystąpienie jest dostarczone w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-512">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="58c5e-513">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="58c5e-513">Call services from main</span></span>

<span data-ttu-id="58c5e-514">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-514">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="58c5e-515">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-515">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="58c5e-516">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="58c5e-516">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="58c5e-517">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="58c5e-517">Scope validation</span></span>

<span data-ttu-id="58c5e-518">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-518">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="58c5e-519">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="58c5e-519">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="58c5e-520">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="58c5e-520">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="58c5e-521">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="58c5e-521">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="58c5e-522">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-522">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="58c5e-523">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="58c5e-523">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="58c5e-524">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="58c5e-524">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="58c5e-525">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="58c5e-525">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="58c5e-526">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="58c5e-526">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="58c5e-527">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="58c5e-527">Request Services</span></span>

<span data-ttu-id="58c5e-528">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="58c5e-528">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="58c5e-529">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-529">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="58c5e-530">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="58c5e-530">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="58c5e-531">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="58c5e-531">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="58c5e-532">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-532">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="58c5e-533">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="58c5e-533">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="58c5e-534">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-534">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="58c5e-535">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="58c5e-535">Design services for dependency injection</span></span>

<span data-ttu-id="58c5e-536">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="58c5e-536">Best practices are to:</span></span>

* <span data-ttu-id="58c5e-537">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-537">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="58c5e-538">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="58c5e-538">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="58c5e-539">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="58c5e-539">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="58c5e-540">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-540">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="58c5e-541">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-541">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="58c5e-542">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="58c5e-542">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="58c5e-543">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="58c5e-543">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="58c5e-544">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-544">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="58c5e-545">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="58c5e-545">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="58c5e-546">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="58c5e-546">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="58c5e-547">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="58c5e-547">Disposal of services</span></span>

<span data-ttu-id="58c5e-548">Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="58c5e-548">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="58c5e-549">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-549">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="58c5e-550">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="58c5e-550">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="58c5e-551">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="58c5e-551">In the following example:</span></span>

* <span data-ttu-id="58c5e-552">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-552">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="58c5e-553">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="58c5e-553">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="58c5e-554">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-554">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="58c5e-555">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="58c5e-555">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="58c5e-556">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="58c5e-556">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="58c5e-557">Przejściowy, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="58c5e-557">Transient, limited lifetime</span></span>

<span data-ttu-id="58c5e-558">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="58c5e-558">**Scenario**</span></span>

<span data-ttu-id="58c5e-559">Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="58c5e-559">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="58c5e-560">Wystąpienie jest rozwiązane w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="58c5e-560">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="58c5e-561">Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.</span><span class="sxs-lookup"><span data-stu-id="58c5e-561">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="58c5e-562">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="58c5e-562">**Solution**</span></span>

<span data-ttu-id="58c5e-563">Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="58c5e-563">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="58c5e-564">W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego.</span><span class="sxs-lookup"><span data-stu-id="58c5e-564">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="58c5e-565">Jeśli typ końcowy ma inne zależności, fabryka może:</span><span class="sxs-lookup"><span data-stu-id="58c5e-565">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="58c5e-566">Odbierz <xref:System.IServiceProvider> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="58c5e-566">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="58c5e-567">Użyj, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-567">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="58c5e-568">Wystąpienie udostępnione, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="58c5e-568">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="58c5e-569">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="58c5e-569">**Scenario**</span></span>

<span data-ttu-id="58c5e-570">Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> powinna mieć ograniczony okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="58c5e-570">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="58c5e-571">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="58c5e-571">**Solution**</span></span>

<span data-ttu-id="58c5e-572">Zarejestruj wystąpienie z okresem istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-572">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="58c5e-573">Użyj, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> Aby rozpocząć i utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="58c5e-573">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="58c5e-574">Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-574">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="58c5e-575">Usuń zakres, gdy okres istnienia powinien zostać zakończony.</span><span class="sxs-lookup"><span data-stu-id="58c5e-575">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="58c5e-576">Ogólne wskazówki</span><span class="sxs-lookup"><span data-stu-id="58c5e-576">General Guidelines</span></span>

* <span data-ttu-id="58c5e-577">Nie rejestruj <xref:System.IDisposable> wystąpień z zakresem przejściowym.</span><span class="sxs-lookup"><span data-stu-id="58c5e-577">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="58c5e-578">Zamiast tego użyj wzorca fabryki.</span><span class="sxs-lookup"><span data-stu-id="58c5e-578">Use the factory pattern instead.</span></span>
* <span data-ttu-id="58c5e-579">Nie należy rozwiązywać wystąpień przejściowych lub zakresów <xref:System.IDisposable> w zakresie w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="58c5e-579">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="58c5e-580">Jedyny ogólny wyjątek polega na tym, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> obiekt, który nie jest idealnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="58c5e-580">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="58c5e-581">Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie.</span><span class="sxs-lookup"><span data-stu-id="58c5e-581">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="58c5e-582">Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.</span><span class="sxs-lookup"><span data-stu-id="58c5e-582">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="58c5e-583">Zakresy powinny służyć do kontrolowania okresów istnienia usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-583">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="58c5e-584">Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.</span><span class="sxs-lookup"><span data-stu-id="58c5e-584">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="58c5e-585">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="58c5e-585">Default service container replacement</span></span>

<span data-ttu-id="58c5e-586">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="58c5e-586">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="58c5e-587">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="58c5e-587">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="58c5e-588">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="58c5e-588">Property injection</span></span>
* <span data-ttu-id="58c5e-589">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="58c5e-589">Injection based on name</span></span>
* <span data-ttu-id="58c5e-590">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="58c5e-590">Child containers</span></span>
* <span data-ttu-id="58c5e-591">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="58c5e-591">Custom lifetime management</span></span>
* <span data-ttu-id="58c5e-592">`Func<T>` Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="58c5e-592">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="58c5e-593">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="58c5e-593">Convention-based registration</span></span>

<span data-ttu-id="58c5e-594">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="58c5e-594">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="58c5e-595">Autofac</span><span class="sxs-lookup"><span data-stu-id="58c5e-595">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="58c5e-596">DryIoc</span><span class="sxs-lookup"><span data-stu-id="58c5e-596">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="58c5e-597">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="58c5e-597">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="58c5e-598">LightInject</span><span class="sxs-lookup"><span data-stu-id="58c5e-598">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="58c5e-599">Lamar</span><span class="sxs-lookup"><span data-stu-id="58c5e-599">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="58c5e-600">Stashbox</span><span class="sxs-lookup"><span data-stu-id="58c5e-600">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="58c5e-601">Unity</span><span class="sxs-lookup"><span data-stu-id="58c5e-601">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="58c5e-602">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="58c5e-602">Thread safety</span></span>

<span data-ttu-id="58c5e-603">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="58c5e-603">Create thread-safe singleton services.</span></span> <span data-ttu-id="58c5e-604">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="58c5e-604">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="58c5e-605">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="58c5e-605">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="58c5e-606">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="58c5e-606">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="58c5e-607">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="58c5e-607">Recommendations</span></span>

* <span data-ttu-id="58c5e-608">`async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="58c5e-608">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="58c5e-609">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-609">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="58c5e-610">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-610">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="58c5e-611">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="58c5e-611">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="58c5e-612">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="58c5e-612">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="58c5e-613">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="58c5e-613">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="58c5e-614">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="58c5e-614">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="58c5e-615">Unikaj statycznego dostępu do usług.</span><span class="sxs-lookup"><span data-stu-id="58c5e-615">Avoid static access to services.</span></span> <span data-ttu-id="58c5e-616">Na przykład należy unikać statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu.</span><span class="sxs-lookup"><span data-stu-id="58c5e-616">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="58c5e-617">Unikaj używania *wzorca lokalizatora usługi* , który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="58c5e-617">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="58c5e-618">Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="58c5e-618">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="58c5e-619">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="58c5e-619">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="58c5e-620">**Poprawne** :</span><span class="sxs-lookup"><span data-stu-id="58c5e-620">**Correct** :</span></span>

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

* <span data-ttu-id="58c5e-621">Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie wykonywania za pomocą polecenia <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="58c5e-621">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="58c5e-622">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="58c5e-622">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="58c5e-623">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="58c5e-623">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="58c5e-624">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="58c5e-624">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="58c5e-625">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="58c5e-625">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="58c5e-626">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="58c5e-626">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58c5e-627">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="58c5e-627">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="58c5e-628">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58c5e-628">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="58c5e-629">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="58c5e-629">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="58c5e-630">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="58c5e-630">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="58c5e-631">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="58c5e-631">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="58c5e-632">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="58c5e-632">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
