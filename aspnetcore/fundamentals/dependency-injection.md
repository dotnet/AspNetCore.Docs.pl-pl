---
title: Wstrzykiwanie zależności w ASP.NET Core
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
ms.openlocfilehash: 99e0109ea4c2526e9f91a8a4df23c4557e9be83a
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762311"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="ee632-103">Wstrzykiwanie zależności w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee632-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ee632-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="ee632-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="ee632-105">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="ee632-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="ee632-106">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="ee632-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="ee632-107">Aby uzyskać więcej informacji na temat wstrzykiwania zależności opcji, zobacz <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="ee632-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="ee632-108">Ten temat zawiera informacje dotyczące iniekcji zależności w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee632-108">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="ee632-109">Aby uzyskać informacje na temat używania iniekcji zależności w aplikacjach konsolowych, zobacz [iniekcja zależności w programie .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ee632-109">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="ee632-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee632-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="ee632-111">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="ee632-111">Overview of dependency injection</span></span>

<span data-ttu-id="ee632-112">*Zależność* jest obiektem, od którego zależy inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="ee632-112">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="ee632-113">Zapoznaj się `MyDependency` z następującą klasą z `WriteMessage` metodą, od której zależą inne klasy:</span><span class="sxs-lookup"><span data-stu-id="ee632-113">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="ee632-114">Klasa może utworzyć wystąpienie `MyDependency` klasy, aby użyć jej `WriteMessage` metody.</span><span class="sxs-lookup"><span data-stu-id="ee632-114">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="ee632-115">W poniższym przykładzie `MyDependency` Klasa jest zależna od `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="ee632-115">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="ee632-116">Klasa tworzy i bezpośrednio zależy od `MyDependency` klasy.</span><span class="sxs-lookup"><span data-stu-id="ee632-116">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="ee632-117">Zależności kodu, takie jak w poprzednim przykładzie, są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="ee632-117">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="ee632-118">Aby zastąpić `MyDependency` inną implementacją, `IndexModel` należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="ee632-118">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="ee632-119">Jeśli `MyDependency` ma zależności, muszą one być również konfigurowane przez `IndexModel` klasę.</span><span class="sxs-lookup"><span data-stu-id="ee632-119">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="ee632-120">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-120">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="ee632-121">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="ee632-121">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ee632-122">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="ee632-122">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="ee632-123">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="ee632-123">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="ee632-124">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-124">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="ee632-125">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-125">Registration of the dependency in a service container.</span></span> <span data-ttu-id="ee632-126">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="ee632-126">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="ee632-127">Usługi są zwykle zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-127">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="ee632-128">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="ee632-128">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ee632-129">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="ee632-129">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="ee632-130">W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje `WriteMessage` metodę:</span><span class="sxs-lookup"><span data-stu-id="ee632-130">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ee632-131">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="ee632-131">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ee632-132">Przykładowa aplikacja rejestruje `IMyDependency` usługę przy użyciu konkretnego typu `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="ee632-132">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ee632-133"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda rejestruje usługę z okresem istnienia w określonym zakresie, okresem istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-133">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="ee632-134">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="ee632-134">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="ee632-135">W przykładowej aplikacji `IMyDependency` jest wymagana usługa, która jest używana do wywołania `WriteMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="ee632-135">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="ee632-136">Przy użyciu wzorca DI, kontroler:</span><span class="sxs-lookup"><span data-stu-id="ee632-136">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="ee632-137">Nie używa konkretnego typu `MyDependency` , tylko `IMyDependency` interfejsu, który implementuje.</span><span class="sxs-lookup"><span data-stu-id="ee632-137">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="ee632-138">Dzięki temu można łatwo zmienić implementację używaną przez kontroler bez modyfikowania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ee632-138">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="ee632-139">Nie tworzy wystąpienia `MyDependency` , jest tworzone przez element di kontener.</span><span class="sxs-lookup"><span data-stu-id="ee632-139">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="ee632-140">Implementację `IMyDependency` interfejsu można ulepszyć za pomocą wbudowanego interfejsu API rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="ee632-140">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="ee632-141">Zaktualizowana `ConfigureServices` Metoda rejestruje nową `IMyDependency` implementację:</span><span class="sxs-lookup"><span data-stu-id="ee632-141">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="ee632-142">`MyDependency2` zależy od <xref:Microsoft.Extensions.Logging.ILogger%601> , który z nich żąda w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="ee632-142">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="ee632-143">`ILogger<TCategoryName>` to [Usługa udostępniona przez platformę](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="ee632-143">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="ee632-144">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="ee632-144">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ee632-145">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-145">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="ee632-146">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-146">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="ee632-147">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="ee632-147">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="ee632-148">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="ee632-148">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="ee632-149">W terminologii wtrysku zależności, usługa:</span><span class="sxs-lookup"><span data-stu-id="ee632-149">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="ee632-150">Jest zazwyczaj obiektem, który udostępnia usługę innym obiektom, takim jak `IMyDependency` Usługa.</span><span class="sxs-lookup"><span data-stu-id="ee632-150">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="ee632-151">Nie jest powiązany z usługą sieci Web, chociaż usługa może korzystać z usługi sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ee632-151">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="ee632-152">Struktura zapewnia niezawodny system [rejestrowania](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="ee632-152">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="ee632-153">`IMyDependency`Implementacje opisane w powyższych przykładach zostały opracowane w celu zademonstrowania podstawowych di, a nie do zaimplementowania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ee632-153">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="ee632-154">Większość aplikacji nie musi być w trakcie pisania rejestratorów.</span><span class="sxs-lookup"><span data-stu-id="ee632-154">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="ee632-155">Poniższy kod ilustruje użycie domyślnego rejestrowania, które nie wymaga zarejestrowania żadnych usług w programie `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee632-155">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="ee632-156">Korzystając z powyższego kodu, nie ma potrzeby aktualizowania `ConfigureServices` , ponieważ [Rejestrowanie](xref:fundamentals/logging/index) jest dostarczane przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="ee632-156">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="ee632-157">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="ee632-157">Services injected into Startup</span></span>

<span data-ttu-id="ee632-158">Usługi można wstrzyknąć do `Startup` konstruktora i `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="ee632-158">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="ee632-159">Tylko następujące usługi mogą być wstrzykiwane do konstruktora w `Startup` przypadku korzystania z hosta ogólnego ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="ee632-159">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ee632-160">Wszystkie usługi zarejestrowane za pomocą DI Container można wstrzyknąć do `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="ee632-160">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="ee632-161">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup> i [Konfigurowanie dostępu podczas uruchamiania](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="ee632-161">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="ee632-162">Rejestrowanie grup usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="ee632-162">Register groups of services with extension methods</span></span>

<span data-ttu-id="ee632-163">ASP.NET Core Framework używa konwencji do rejestracji grupy powiązanych usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-163">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="ee632-164">Konwencja polega na użyciu pojedynczej `Add{GROUP_NAME}` metody rozszerzenia w celu zarejestrowania wszystkich usług wymaganych przez funkcję platformy.</span><span class="sxs-lookup"><span data-stu-id="ee632-164">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="ee632-165">Na przykład <Microsoft. Extensions. DependencyInjection. MvcServiceCollectionExtensions. addcontrollers> Metoda rozszerzenia rejestruje usługi wymagane przez kontrolery MVC.</span><span class="sxs-lookup"><span data-stu-id="ee632-165">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="ee632-166">Poniższy kod jest generowany przez Razor szablon stron przy użyciu poszczególnych kont użytkowników i pokazuje, jak dodać dodatkowe usługi do kontenera przy użyciu metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="ee632-166">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="ee632-167">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="ee632-167">Service lifetimes</span></span>

<span data-ttu-id="ee632-168">Usługi mogą być zarejestrowane przy użyciu jednego z następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="ee632-168">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="ee632-169">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-169">Transient</span></span>
* <span data-ttu-id="ee632-170">Zakresie</span><span class="sxs-lookup"><span data-stu-id="ee632-170">Scoped</span></span>
* <span data-ttu-id="ee632-171">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-171">Singleton</span></span>

<span data-ttu-id="ee632-172">W poniższych sekcjach opisano wszystkie poprzednie okresy istnienia.</span><span class="sxs-lookup"><span data-stu-id="ee632-172">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="ee632-173">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-173">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="ee632-174">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-174">Transient</span></span>

<span data-ttu-id="ee632-175">Przejściowe usługi okresu istnienia są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-175">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="ee632-176">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-176">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="ee632-177">Zarejestruj usługi przejściowe w usłudze <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="ee632-177">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="ee632-178">W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-178">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="ee632-179">Zakresie</span><span class="sxs-lookup"><span data-stu-id="ee632-179">Scoped</span></span>

<span data-ttu-id="ee632-180">Usługi okresu istnienia w zakresie są tworzone raz dla żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="ee632-180">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="ee632-181">Zarejestruj usługi z zakresem w usłudze <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="ee632-181">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="ee632-182">W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-182">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="ee632-183">W przypadku korzystania z Entity Framework Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Metoda rozszerzenia rejestruje `DbContext` typy z okresem istnienia w zakresie domyślnie.</span><span class="sxs-lookup"><span data-stu-id="ee632-183">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="ee632-184">***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="ee632-184">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="ee632-185">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="ee632-185">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="ee632-186">Warto:</span><span class="sxs-lookup"><span data-stu-id="ee632-186">It's fine to:</span></span>

* <span data-ttu-id="ee632-187">Rozwiązanie pojedynczej usługi z usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="ee632-187">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="ee632-188">Rozwiąż usługę objętą zakresem z innej usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="ee632-188">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="ee632-189">Domyślnie w środowisku programistycznym rozpoznawanie usługi z innej usługi o dłuższym okresie istnienia zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="ee632-189">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="ee632-190">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#sv).</span><span class="sxs-lookup"><span data-stu-id="ee632-190">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="ee632-191">Aby korzystać z usług objętych zakresem w oprogramowaniu pośredniczącym, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ee632-191">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="ee632-192">Wsuń usługę do programu pośredniczącego `Invoke` lub `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="ee632-192">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ee632-193">Użycie [iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) zgłasza wyjątek czasu wykonywania, ponieważ wymusza, aby usługa w zakresie działała jak pojedyncza.</span><span class="sxs-lookup"><span data-stu-id="ee632-193">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="ee632-194">Przykład w sekcji [Opcje czasu istnienia i rejestracji](#lifetime-and-registration-options) przedstawia `InvokeAsync` podejście.</span><span class="sxs-lookup"><span data-stu-id="ee632-194">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="ee632-195">Używaj [oprogramowania pośredniczącego opartego na fabryce](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="ee632-195">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="ee632-196">Oprogramowanie pośredniczące zarejestrowane przy użyciu tego podejścia jest aktywowane na żądanie klienta (połączenie), które umożliwia wprowadzanie w zakresie usług objętych zakresem do metody oprogramowania pośredniczącego `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="ee632-196">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="ee632-197">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="ee632-197">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="ee632-198">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-198">Singleton</span></span>

<span data-ttu-id="ee632-199">Są tworzone pojedyncze usługi okresu istnienia:</span><span class="sxs-lookup"><span data-stu-id="ee632-199">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="ee632-200">Podczas pierwszego żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-200">The first time they're requested.</span></span>
* <span data-ttu-id="ee632-201">Przez dewelopera, gdy udostępnia wystąpienie implementacji bezpośrednio do kontenera.</span><span class="sxs-lookup"><span data-stu-id="ee632-201">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="ee632-202">Takie podejście jest rzadko niezbędne.</span><span class="sxs-lookup"><span data-stu-id="ee632-202">This approach is rarely needed.</span></span>

<span data-ttu-id="ee632-203">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ee632-203">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="ee632-204">Jeśli aplikacja wymaga pojedynczych zachowań, zezwól kontenerowi usługi na zarządzanie okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-204">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="ee632-205">Nie Wdrażaj wzorca projektu singleton i podawanie kodu do usuwania pojedynczych elementów.</span><span class="sxs-lookup"><span data-stu-id="ee632-205">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="ee632-206">Usługi nigdy nie powinny być usuwane przez kod, który rozwiązał usługę z kontenera.</span><span class="sxs-lookup"><span data-stu-id="ee632-206">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="ee632-207">Jeśli typ lub fabryka są zarejestrowane jako pojedyncze, kontener usuwa pojedyncze automatycznie.</span><span class="sxs-lookup"><span data-stu-id="ee632-207">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="ee632-208">Zarejestruj usługi pojedyncze przy użyciu programu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="ee632-208">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="ee632-209">Usługi pojedyncze muszą być bezpieczne dla wątków i często używane w usługach bezstanowych.</span><span class="sxs-lookup"><span data-stu-id="ee632-209">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="ee632-210">W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-210">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="ee632-211">Ponieważ pamięć nie jest wydawana do momentu wyłączenia aplikacji, należy rozważyć użycie pamięci w ramach pojedynczej usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-211">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="ee632-212">***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="ee632-212">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="ee632-213">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="ee632-213">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="ee632-214">Rozwiązaniem jest rozwiązanie pojedynczej usługi z poziomu usługi w zakresie lub przejściowej.</span><span class="sxs-lookup"><span data-stu-id="ee632-214">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ee632-215">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="ee632-215">Service registration methods</span></span>

<span data-ttu-id="ee632-216">Struktura zawiera metody rozszerzenia rejestracji usług, które są przydatne w określonych scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="ee632-216">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="ee632-217">Metoda</span><span class="sxs-lookup"><span data-stu-id="ee632-217">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="ee632-218">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="ee632-218">Automatic</span></span><br><span data-ttu-id="ee632-219">object</span><span class="sxs-lookup"><span data-stu-id="ee632-219">object</span></span><br><span data-ttu-id="ee632-220">myśl</span><span class="sxs-lookup"><span data-stu-id="ee632-220">disposal</span></span> | <span data-ttu-id="ee632-221">Wiele</span><span class="sxs-lookup"><span data-stu-id="ee632-221">Multiple</span></span><br><span data-ttu-id="ee632-222">implementacje</span><span class="sxs-lookup"><span data-stu-id="ee632-222">implementations</span></span> | <span data-ttu-id="ee632-223">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="ee632-223">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ee632-224">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ee632-224">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="ee632-225">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-225">Yes</span></span>                             | <span data-ttu-id="ee632-226">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-226">Yes</span></span>                         | <span data-ttu-id="ee632-227">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-227">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ee632-228">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="ee632-228">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="ee632-229">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-229">Yes</span></span>                             | <span data-ttu-id="ee632-230">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-230">Yes</span></span>                         | <span data-ttu-id="ee632-231">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-231">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ee632-232">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ee632-232">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="ee632-233">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-233">Yes</span></span>                             | <span data-ttu-id="ee632-234">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-234">No</span></span>                          | <span data-ttu-id="ee632-235">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-235">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ee632-236">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="ee632-236">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="ee632-237">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-237">No</span></span>                              | <span data-ttu-id="ee632-238">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-238">Yes</span></span>                         | <span data-ttu-id="ee632-239">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-239">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ee632-240">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="ee632-240">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="ee632-241">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-241">No</span></span>                              | <span data-ttu-id="ee632-242">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-242">No</span></span>                          | <span data-ttu-id="ee632-243">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-243">Yes</span></span>       |

<span data-ttu-id="ee632-244">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="ee632-244">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="ee632-245">Typowym sposobem użycia wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="ee632-245">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="ee632-246">Struktura zawiera również `TryAdd{LIFETIME}` metody rozszerzające, które rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-246">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="ee632-247">W poniższym przykładzie wywołanie `AddSingleton` rejestruje się `MyDependency` jako implementacja dla `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="ee632-247">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="ee632-248">Wywołanie nie `TryAddSingleton` działa, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="ee632-248">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="ee632-249">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="ee632-249">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="ee632-250">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="ee632-250">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ee632-251">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="ee632-251">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="ee632-252">Podczas rejestrowania usług deweloper powinien dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="ee632-252">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="ee632-253">Zazwyczaj autorzy biblioteki używają, `TryAddEnumerable` Aby uniknąć rejestrowania wielu kopii implementacji w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="ee632-253">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="ee632-254">W poniższym przykładzie pierwsze wywołanie `TryAddEnumerable` rejestracji `MyDependency` jako implementacji dla `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="ee632-254">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="ee632-255">Drugie wywołanie rejestru `MyDependency` dla `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="ee632-255">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="ee632-256">Trzecie wywołanie nie działa `IMyDependency1` , ponieważ ma już zarejestrowana implementacja `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="ee632-256">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="ee632-257">Rejestracja usługi jest zazwyczaj kolejnością niezależną, z wyjątkiem rejestracji wielu implementacji tego samego typu.</span><span class="sxs-lookup"><span data-stu-id="ee632-257">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="ee632-258">`IServiceCollection` jest kolekcją <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> obiektów.</span><span class="sxs-lookup"><span data-stu-id="ee632-258">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="ee632-259">Poniższy przykład pokazuje, jak zarejestrować usługę przez utworzenie i dodanie `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="ee632-259">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="ee632-260">`Add{LIFETIME}`Metody wbudowane wykorzystują takie samo podejście.</span><span class="sxs-lookup"><span data-stu-id="ee632-260">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="ee632-261">Na przykład zobacz [kod źródłowy addscope](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="ee632-261">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ee632-262">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="ee632-262">Constructor injection behavior</span></span>

<span data-ttu-id="ee632-263">Usługi można rozwiązać przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="ee632-263">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ee632-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="ee632-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="ee632-265">Tworzy obiekty, które nie są zarejestrowane w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="ee632-265">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="ee632-266">Używany z funkcjami platformy, takimi jak [pomocnicy tagów](xref:mvc/views/tag-helpers/intro), kontrolery MVC i [powiązania modeli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="ee632-266">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="ee632-267">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="ee632-267">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="ee632-268">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="ee632-268">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="ee632-269">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="ee632-269">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="ee632-270">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-270">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ee632-271">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ee632-271">Entity Framework contexts</span></span>

<span data-ttu-id="ee632-272">Domyślnie kontekstu Entity Framework są dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle objęte zakresem żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="ee632-272">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="ee632-273">Aby użyć innego okresu istnienia, należy określić okres istnienia przy użyciu <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> przeciążenia.</span><span class="sxs-lookup"><span data-stu-id="ee632-273">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="ee632-274">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z okresem istnienia krótszym niż okres istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-274">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ee632-275">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="ee632-275">Lifetime and registration options</span></span>

<span data-ttu-id="ee632-276">Aby zademonstrować różnicę między okresami istnienia usługi a ich opcjami rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadanie jako operację z identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="ee632-276">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="ee632-277">W zależności od sposobu skonfigurowania okresu istnienia usługi dla następujących interfejsów kontener zawiera te same lub inne wystąpienia usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="ee632-277">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ee632-278">W poniższej `Operation` klasie zostały zaimplementowane wszystkie poprzednie interfejsy.</span><span class="sxs-lookup"><span data-stu-id="ee632-278">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="ee632-279">`Operation`Konstruktor generuje identyfikator GUID i przechowuje 4 ostatnie znaki we `OperationId` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="ee632-279">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="ee632-280">`Startup.ConfigureServices`Metoda tworzy wiele rejestracji `Operation` klasy zgodnie z nazwanymi okresami istnienia:</span><span class="sxs-lookup"><span data-stu-id="ee632-280">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="ee632-281">Przykładowa aplikacja pokazuje okresy istnienia obiektów zarówno w ramach żądań, jak i między nimi.</span><span class="sxs-lookup"><span data-stu-id="ee632-281">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="ee632-282">`IndexModel`I oprogramowanie pośredniczące żąda każdego rodzaju `IOperation` typu i rejestruje `OperationId` dla każdej z nich:</span><span class="sxs-lookup"><span data-stu-id="ee632-282">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="ee632-283">Podobnie jak w programie `IndexModel` , oprogramowanie pośredniczące rozwiązuje te same usługi:</span><span class="sxs-lookup"><span data-stu-id="ee632-283">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="ee632-284">Usługi w zakresie muszą zostać rozwiązane w `InvokeAsync` metodzie:</span><span class="sxs-lookup"><span data-stu-id="ee632-284">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="ee632-285">Dane wyjściowe rejestratora przedstawiają:</span><span class="sxs-lookup"><span data-stu-id="ee632-285">The logger output shows:</span></span>

* <span data-ttu-id="ee632-286">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="ee632-286">*Transient* objects are always different.</span></span> <span data-ttu-id="ee632-287">Wartość przejściowa `OperationId` różni się w `IndexModel` i w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="ee632-287">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="ee632-288">Obiekty o określonym *zakresie* są takie same dla każdego żądania, ale różnią się w zależności od każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-288">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="ee632-289">*Pojedyncze* obiekty są takie same dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-289">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="ee632-290">Aby zmniejszyć ilość danych wyjściowych rejestrowania, ustaw wartość opcji "Rejestrowanie: LogLevel: Microsoft: Error" w *appsettings.Development.js* w pliku:</span><span class="sxs-lookup"><span data-stu-id="ee632-290">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="ee632-291">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="ee632-291">Call services from main</span></span>

<span data-ttu-id="ee632-292">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-292">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="ee632-293">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="ee632-293">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="ee632-294">Poniższy przykład pokazuje, jak uzyskać dostęp do `IMyDependency` usługi w zakresie i wywołać jej `WriteMessage` metodę w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="ee632-294">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="ee632-295">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="ee632-295">Scope validation</span></span>

<span data-ttu-id="ee632-296">Gdy aplikacja jest uruchamiana w [środowisku deweloperskim](xref:fundamentals/environments) i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do skompilowania hosta, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="ee632-296">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ee632-297">Usługi w zakresie nie są rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="ee632-297">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="ee632-298">Usługi w zakresie nie są wstawiane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="ee632-298">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="ee632-299">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="ee632-299">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="ee632-300">Okres istnienia dostawcy usług głównych odpowiada okresowi istnienia aplikacji, gdy dostawca rozpoczyna pracę z aplikacją i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-300">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ee632-301">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="ee632-301">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ee632-302">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-302">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="ee632-303">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="ee632-303">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ee632-304">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="ee632-304">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="ee632-305">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="ee632-305">Request Services</span></span>

<span data-ttu-id="ee632-306">Usługi dostępne w ramach żądania ASP.NET Core są udostępniane za pomocą kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="ee632-306">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="ee632-307">Gdy usługi są żądane w ramach żądania, usługi i ich zależności są rozwiązywane z `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="ee632-307">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="ee632-308">Struktura tworzy zakres na żądanie i `RequestServices` uwidacznia dostawcę usług objętych zakresem.</span><span class="sxs-lookup"><span data-stu-id="ee632-308">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="ee632-309">Wszystkie usługi objęte zakresem są prawidłowe przez cały czas, gdy żądanie jest aktywne.</span><span class="sxs-lookup"><span data-stu-id="ee632-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="ee632-310">Preferuj żądania zależności jako parametry konstruktora do rozpoznawania usług z `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="ee632-310">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="ee632-311">Dzięki temu klasy są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="ee632-311">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ee632-312">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="ee632-312">Design services for dependency injection</span></span>

<span data-ttu-id="ee632-313">Podczas projektowania usług pod kątem iniekcji zależności:</span><span class="sxs-lookup"><span data-stu-id="ee632-313">When designing services for dependency injection:</span></span>

* <span data-ttu-id="ee632-314">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="ee632-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ee632-315">Należy unikać tworzenia stanu globalnego przez projektowanie aplikacji do korzystania z usług pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="ee632-315">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="ee632-316">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ee632-317">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="ee632-318">Zapewnianie niewielkich, dobrze przeprowadzonych i łatwych do przetestowania usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-318">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ee632-319">Jeśli klasa ma dużo wstrzykiwanych zależności, może być znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="ee632-319">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ee632-320">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowych klas.</span><span class="sxs-lookup"><span data-stu-id="ee632-320">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="ee632-321">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ee632-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ee632-322">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="ee632-322">Disposal of services</span></span>

<span data-ttu-id="ee632-323">Kontener wywołuje <xref:System.IDisposable.Dispose%2A> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="ee632-323">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="ee632-324">Usługi rozpoznane przez kontener nigdy nie powinny zostać usunięte przez dewelopera.</span><span class="sxs-lookup"><span data-stu-id="ee632-324">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="ee632-325">Jeśli typ lub fabryka są zarejestrowane jako pojedyncze, kontener usuwa pojedyncze automatycznie.</span><span class="sxs-lookup"><span data-stu-id="ee632-325">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="ee632-326">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="ee632-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ee632-327">Konsola debugowania pokazuje następujące dane wyjściowe po każdym odświeżeniu strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="ee632-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="ee632-328">Usługi, które nie zostały utworzone przez kontener usługi</span><span class="sxs-lookup"><span data-stu-id="ee632-328">Services not created by the service container</span></span>

<span data-ttu-id="ee632-329">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="ee632-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="ee632-330">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ee632-330">In the preceding code:</span></span>

* <span data-ttu-id="ee632-331">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="ee632-332">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-332">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="ee632-333">Deweloper jest odpowiedzialny za likwidację usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-333">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="ee632-334">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="ee632-334">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="ee632-335">Przejściowy, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-335">Transient, limited lifetime</span></span>

<span data-ttu-id="ee632-336">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="ee632-336">**Scenario**</span></span>

<span data-ttu-id="ee632-337">Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="ee632-337">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="ee632-338">Wystąpienie jest rozwiązane w zakresie głównym (kontener główny).</span><span class="sxs-lookup"><span data-stu-id="ee632-338">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="ee632-339">Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.</span><span class="sxs-lookup"><span data-stu-id="ee632-339">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="ee632-340">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="ee632-340">**Solution**</span></span>

<span data-ttu-id="ee632-341">Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="ee632-341">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="ee632-342">W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego.</span><span class="sxs-lookup"><span data-stu-id="ee632-342">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="ee632-343">Jeśli typ końcowy ma inne zależności, fabryka może:</span><span class="sxs-lookup"><span data-stu-id="ee632-343">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="ee632-344">Odbierz <xref:System.IServiceProvider> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="ee632-344">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="ee632-345">Użyj polecenia, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-345">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="ee632-346">Wystąpienie udostępnione, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-346">Shared instance, limited lifetime</span></span>

<span data-ttu-id="ee632-347">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="ee632-347">**Scenario**</span></span>

<span data-ttu-id="ee632-348">Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> wystąpienie powinno mieć ograniczony okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="ee632-348">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="ee632-349">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="ee632-349">**Solution**</span></span>

<span data-ttu-id="ee632-350">Zarejestruj wystąpienie z okresem istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="ee632-350">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="ee632-351">Użyj <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> , aby utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="ee632-351">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="ee632-352">Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-352">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="ee632-353">Usuń zakres, gdy nie jest już wymagany.</span><span class="sxs-lookup"><span data-stu-id="ee632-353">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="ee632-354">Ogólne wytyczne interfejsu IDisposable</span><span class="sxs-lookup"><span data-stu-id="ee632-354">General IDisposable guidelines</span></span>

* <span data-ttu-id="ee632-355">Nie rejestruj <xref:System.IDisposable> wystąpień z przejściowym okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="ee632-355">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="ee632-356">Zamiast tego użyj wzorca fabryki.</span><span class="sxs-lookup"><span data-stu-id="ee632-356">Use the factory pattern instead.</span></span>
* <span data-ttu-id="ee632-357">Nie należy rozwiązywać <xref:System.IDisposable> wystąpień z okresem przejściowym lub z określonym zakresem w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="ee632-357">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="ee632-358">Jedynym wyjątkiem jest to, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> , ale nie jest idealnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="ee632-358">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="ee632-359">Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie.</span><span class="sxs-lookup"><span data-stu-id="ee632-359">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="ee632-360">Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-360">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="ee632-361">Używanie zakresów do kontrolowania okresów istnienia usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-361">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="ee632-362">Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.</span><span class="sxs-lookup"><span data-stu-id="ee632-362">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="ee632-363">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="ee632-363">Default service container replacement</span></span>

<span data-ttu-id="ee632-364">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="ee632-364">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="ee632-365">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest określona funkcja, która nie jest obsługiwana przez program, na przykład:</span><span class="sxs-lookup"><span data-stu-id="ee632-365">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="ee632-366">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="ee632-366">Property injection</span></span>
* <span data-ttu-id="ee632-367">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="ee632-367">Injection based on name</span></span>
* <span data-ttu-id="ee632-368">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="ee632-368">Child containers</span></span>
* <span data-ttu-id="ee632-369">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-369">Custom lifetime management</span></span>
* <span data-ttu-id="ee632-370">`Func<T>` Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="ee632-370">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ee632-371">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="ee632-371">Convention-based registration</span></span>

<span data-ttu-id="ee632-372">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="ee632-372">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="ee632-373">Autofac</span><span class="sxs-lookup"><span data-stu-id="ee632-373">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="ee632-374">DryIoc</span><span class="sxs-lookup"><span data-stu-id="ee632-374">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="ee632-375">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="ee632-375">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="ee632-376">LightInject</span><span class="sxs-lookup"><span data-stu-id="ee632-376">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="ee632-377">Lamar</span><span class="sxs-lookup"><span data-stu-id="ee632-377">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="ee632-378">Stashbox</span><span class="sxs-lookup"><span data-stu-id="ee632-378">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="ee632-379">Unity</span><span class="sxs-lookup"><span data-stu-id="ee632-379">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="ee632-380">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="ee632-380">Thread safety</span></span>

<span data-ttu-id="ee632-381">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="ee632-381">Create thread-safe singleton services.</span></span> <span data-ttu-id="ee632-382">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątków w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="ee632-382">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="ee632-383">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="ee632-383">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ee632-384">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie tylko raz przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="ee632-384">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ee632-385">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="ee632-385">Recommendations</span></span>

* <span data-ttu-id="ee632-386">`async/await``Task`rozpoznawanie usług opartych na usłudze i nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="ee632-386">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="ee632-387">Ponieważ język C# nie obsługuje konstruktorów asynchronicznych, należy używać metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-387">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="ee632-388">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-388">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="ee632-389">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-389">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="ee632-390">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ee632-390">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ee632-391">Podobnie należy unikać obiektów "posiadacz danych", które istnieją tylko w celu zezwalania na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="ee632-391">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="ee632-392">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="ee632-392">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="ee632-393">Unikaj statycznego dostępu do usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-393">Avoid static access to services.</span></span> <span data-ttu-id="ee632-394">Na przykład Unikaj przechwytywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) jako statycznego pola lub właściwości do użycia w innym miejscu.</span><span class="sxs-lookup"><span data-stu-id="ee632-394">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="ee632-395">Utrzymuj szybkie i synchroniczne operacje.</span><span class="sxs-lookup"><span data-stu-id="ee632-395">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="ee632-396">Unikaj używania *wzorca lokalizatora usługi*.</span><span class="sxs-lookup"><span data-stu-id="ee632-396">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="ee632-397">Na przykład nie wywołuj, <xref:System.IServiceProvider.GetService%2A> Aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="ee632-397">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="ee632-398">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="ee632-398">**Incorrect:**</span></span>

    ![Niepoprawny kod](dependency-injection/_static/bad.png)

  <span data-ttu-id="ee632-400">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="ee632-400">**Correct**:</span></span>

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
* <span data-ttu-id="ee632-401">Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ee632-401">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="ee632-402">Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="ee632-402">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="ee632-403">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="ee632-403">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="ee632-404">Należy unikać wywołań do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee632-404">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="ee632-405">Wywoływanie `BuildServiceProvider` zazwyczaj odbywa się, gdy deweloper chce rozwiązać usługę w programie `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee632-405">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="ee632-406">Rozważmy na przykład przypadek, w którym `LoginPath` został załadowany z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ee632-406">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="ee632-407">Należy unikać następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ee632-407">Avoid the following approach:</span></span>

  ![zły kod wywołujący BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="ee632-409">Na powyższym obrazie, wybierając zieloną linię falistą w obszarze `services.BuildServiceProvider` pokazuje następujące ostrzeżenie ASP0000:</span><span class="sxs-lookup"><span data-stu-id="ee632-409">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="ee632-410">ASP0000 wywołanie "BuildServiceProvider" z kodu aplikacji spowoduje utworzenie dodatkowej kopii pojedynczych usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-410">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="ee632-411">Należy rozważyć alternatywy, takie jak "wstrzyknięcie usług" jako parametry do "configure".</span><span class="sxs-lookup"><span data-stu-id="ee632-411">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="ee632-412">Wywołanie `BuildServiceProvider` tworzy drugi kontener, który może tworzyć niezerwane pojedyncze i spowodować odwołania do grafów obiektów w wielu kontenerach.</span><span class="sxs-lookup"><span data-stu-id="ee632-412">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="ee632-413">Prawidłowym sposobem uzyskania `LoginPath` jest użycie wbudowanego wsparcia wzorca opcji dla di:</span><span class="sxs-lookup"><span data-stu-id="ee632-413">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="ee632-414">Nierozporządzalne usługi przejściowe są przechwytywane przez kontener do usuwania.</span><span class="sxs-lookup"><span data-stu-id="ee632-414">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="ee632-415">Może to obsłużyć przeciek pamięci, jeśli został rozwiązany z kontenera najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="ee632-415">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="ee632-416">Włącz weryfikację zakresu, aby upewnić się, że aplikacja nie ma pojedynczych, które przechwytują usługi o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="ee632-416">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="ee632-417">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="ee632-417">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="ee632-418">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="ee632-418">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="ee632-419">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="ee632-419">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="ee632-420">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="ee632-420">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="ee632-421">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="ee632-421">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="ee632-422">Zalecane wzorce dla wielu dzierżawców w programie DI</span><span class="sxs-lookup"><span data-stu-id="ee632-422">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="ee632-423">Element [sadu Core](https://github.com/OrchardCMS/OrchardCore) to struktura aplikacji służąca do tworzenia modularnych aplikacji wielodostępnych na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee632-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="ee632-424">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją podstawową programu sadu](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="ee632-424">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="ee632-425">Zapoznaj się z [podstawowymi](https://github.com/OrchardCMS/OrchardCore.Samples) przykładami sadu, aby poznać przykłady tworzenia aplikacji modularnych i wielodostępnych przy użyciu tylko podstawowej platformy sadu bez żadnych funkcji specyficznych dla usługi CMS.</span><span class="sxs-lookup"><span data-stu-id="ee632-425">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ee632-426">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="ee632-426">Framework-provided services</span></span>

<span data-ttu-id="ee632-427">`Startup.ConfigureServices`Metoda rejestruje usługi używane przez aplikację, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ee632-427">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="ee632-428">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ee632-428">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="ee632-429">W przypadku aplikacji opartych na szablonach ASP.NET Core struktura rejestruje więcej niż 250 usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-429">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="ee632-430">W poniższej tabeli przedstawiono niewielki przykład następujących usług zarejestrowanych w ramach platformy:</span><span class="sxs-lookup"><span data-stu-id="ee632-430">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="ee632-431">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="ee632-431">Service Type</span></span>                                                                                    | <span data-ttu-id="ee632-432">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-432">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ee632-433">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-433">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="ee632-434">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="ee632-435">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="ee632-436">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="ee632-437">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-437">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="ee632-438">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="ee632-439">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-439">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="ee632-440">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="ee632-441">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="ee632-442">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="ee632-443">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-443">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="ee632-444">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="ee632-445">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-445">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="ee632-446">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-446">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="ee632-447">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ee632-447">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="ee632-448">Wzorce konferencji NDC na potrzeby tworzenia aplikacji</span><span class="sxs-lookup"><span data-stu-id="ee632-448">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="ee632-449">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee632-449">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="ee632-450">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="ee632-450">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="ee632-451">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="ee632-451">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="ee632-452">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="ee632-452">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="ee632-453">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="ee632-453">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ee632-454">[Steve Kowalski](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="ee632-454">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="ee632-455">ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.</span><span class="sxs-lookup"><span data-stu-id="ee632-455">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="ee632-456">Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="ee632-456">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="ee632-457">[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee632-457">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="ee632-458">Przegląd iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="ee632-458">Overview of dependency injection</span></span>

<span data-ttu-id="ee632-459">*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="ee632-459">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="ee632-460">Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ee632-460">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="ee632-461">Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie.</span><span class="sxs-lookup"><span data-stu-id="ee632-461">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="ee632-462">`MyDependency`Klasa jest zależnością `IndexModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="ee632-462">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="ee632-463">Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ee632-463">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="ee632-464">Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="ee632-464">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="ee632-465">Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.</span><span class="sxs-lookup"><span data-stu-id="ee632-465">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="ee632-466">Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę.</span><span class="sxs-lookup"><span data-stu-id="ee632-466">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="ee632-467">W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-467">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="ee632-468">Ta implementacja jest trudna do testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="ee632-468">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ee632-469">Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="ee632-469">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="ee632-470">Iniekcja zależności eliminuje te problemy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="ee632-470">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="ee632-471">Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-471">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="ee632-472">Rejestracja zależności w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-472">Registration of the dependency in a service container.</span></span> <span data-ttu-id="ee632-473">ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="ee632-473">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="ee632-474">Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-474">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="ee632-475">*Iniekcja* usługi do konstruktora klasy, w której jest używana.</span><span class="sxs-lookup"><span data-stu-id="ee632-475">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ee632-476">Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.</span><span class="sxs-lookup"><span data-stu-id="ee632-476">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="ee632-477">W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ee632-477">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ee632-478">Ten interfejs jest implementowany przez konkretny typ `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="ee632-478">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ee632-479">`MyDependency` żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="ee632-479">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="ee632-480">Użycie iniekcji zależności w łańcuchu nie jest nietypowe.</span><span class="sxs-lookup"><span data-stu-id="ee632-480">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ee632-481">Każda żądana zależność z kolei żąda własnych zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-481">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="ee632-482">Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-482">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="ee632-483">Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.</span><span class="sxs-lookup"><span data-stu-id="ee632-483">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="ee632-484">`IMyDependency` i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-484">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="ee632-485">`IMyDependency` jest zarejestrowany w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee632-485">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee632-486">`ILogger<TCategoryName>` jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.</span><span class="sxs-lookup"><span data-stu-id="ee632-486">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="ee632-487">Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="ee632-487">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="ee632-488">W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="ee632-488">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ee632-489">Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-489">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="ee632-490">[Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="ee632-490">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="ee632-491">Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje).</span><span class="sxs-lookup"><span data-stu-id="ee632-491">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="ee632-492">Na przykład `services.AddMvc()` dodaje Razor strony usług i wymagane przez MVC.</span><span class="sxs-lookup"><span data-stu-id="ee632-492">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="ee632-493">Zalecamy, aby aplikacje były zgodne z tą konwencją.</span><span class="sxs-lookup"><span data-stu-id="ee632-493">We recommended that apps follow this convention.</span></span> <span data-ttu-id="ee632-494">Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-494">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="ee632-495">Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string` ,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="ee632-495">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="ee632-496">Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="ee632-496">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="ee632-497">To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="ee632-497">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="ee632-498">W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:</span><span class="sxs-lookup"><span data-stu-id="ee632-498">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="ee632-499">Usługi wstrzykiwane do uruchamiania</span><span class="sxs-lookup"><span data-stu-id="ee632-499">Services injected into Startup</span></span>

<span data-ttu-id="ee632-500">Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="ee632-500">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ee632-501">Usługi można wstrzyknąć do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="ee632-501">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="ee632-502">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ee632-502">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ee632-503">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="ee632-503">Framework-provided services</span></span>

<span data-ttu-id="ee632-504">`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ee632-504">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="ee632-505">Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ee632-505">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="ee632-506">Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę.</span><span class="sxs-lookup"><span data-stu-id="ee632-506">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="ee632-507">W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.</span><span class="sxs-lookup"><span data-stu-id="ee632-507">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="ee632-508">Typ usługi</span><span class="sxs-lookup"><span data-stu-id="ee632-508">Service Type</span></span> | <span data-ttu-id="ee632-509">Okres istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-509">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ee632-510">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-510">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="ee632-511">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="ee632-512">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="ee632-513">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="ee632-514">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-514">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="ee632-515">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="ee632-516">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-516">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="ee632-517">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="ee632-518">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="ee632-519">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="ee632-520">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-520">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="ee632-521">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="ee632-522">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="ee632-523">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-523">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="ee632-524">Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających</span><span class="sxs-lookup"><span data-stu-id="ee632-524">Register additional services with extension methods</span></span>

<span data-ttu-id="ee632-525">Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać pojedynczej `Add{SERVICE_NAME}` metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę.</span><span class="sxs-lookup"><span data-stu-id="ee632-525">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="ee632-526">Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="ee632-526">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="ee632-527">Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="ee632-527">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="ee632-528">Okresy istnienia usługi</span><span class="sxs-lookup"><span data-stu-id="ee632-528">Service lifetimes</span></span>

<span data-ttu-id="ee632-529">Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-529">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="ee632-530">Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:</span><span class="sxs-lookup"><span data-stu-id="ee632-530">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="ee632-531">Administracyjnej</span><span class="sxs-lookup"><span data-stu-id="ee632-531">Transient</span></span>

<span data-ttu-id="ee632-532">Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-532">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="ee632-533">Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-533">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="ee632-534">W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-534">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="ee632-535">Zakresie</span><span class="sxs-lookup"><span data-stu-id="ee632-535">Scoped</span></span>

<span data-ttu-id="ee632-536">Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).</span><span class="sxs-lookup"><span data-stu-id="ee632-536">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="ee632-537">W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="ee632-537">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="ee632-538">W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` `InvokeAsync` metody lub.</span><span class="sxs-lookup"><span data-stu-id="ee632-538">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ee632-539">Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej.</span><span class="sxs-lookup"><span data-stu-id="ee632-539">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="ee632-540">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="ee632-540">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="ee632-541">Pojedynczego</span><span class="sxs-lookup"><span data-stu-id="ee632-541">Singleton</span></span>

<span data-ttu-id="ee632-542">Pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi).</span><span class="sxs-lookup"><span data-stu-id="ee632-542">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="ee632-543">Każde kolejne żądanie używa tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="ee632-543">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="ee632-544">Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-544">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="ee632-545">Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.</span><span class="sxs-lookup"><span data-stu-id="ee632-545">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="ee632-546">W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-546">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="ee632-547">Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="ee632-547">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="ee632-548">Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.</span><span class="sxs-lookup"><span data-stu-id="ee632-548">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ee632-549">Metody rejestracji usług</span><span class="sxs-lookup"><span data-stu-id="ee632-549">Service registration methods</span></span>

<span data-ttu-id="ee632-550">Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="ee632-550">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="ee632-551">Metoda</span><span class="sxs-lookup"><span data-stu-id="ee632-551">Method</span></span> | <span data-ttu-id="ee632-552">Automatyczny</span><span class="sxs-lookup"><span data-stu-id="ee632-552">Automatic</span></span><br><span data-ttu-id="ee632-553">object</span><span class="sxs-lookup"><span data-stu-id="ee632-553">object</span></span><br><span data-ttu-id="ee632-554">myśl</span><span class="sxs-lookup"><span data-stu-id="ee632-554">disposal</span></span> | <span data-ttu-id="ee632-555">Wiele</span><span class="sxs-lookup"><span data-stu-id="ee632-555">Multiple</span></span><br><span data-ttu-id="ee632-556">implementacje</span><span class="sxs-lookup"><span data-stu-id="ee632-556">implementations</span></span> | <span data-ttu-id="ee632-557">Przekaż argumenty</span><span class="sxs-lookup"><span data-stu-id="ee632-557">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ee632-558">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ee632-558">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="ee632-559">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-559">Yes</span></span> | <span data-ttu-id="ee632-560">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-560">Yes</span></span> | <span data-ttu-id="ee632-561">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-561">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ee632-562">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="ee632-562">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="ee632-563">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-563">Yes</span></span> | <span data-ttu-id="ee632-564">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-564">Yes</span></span> | <span data-ttu-id="ee632-565">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-565">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ee632-566">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ee632-566">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="ee632-567">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-567">Yes</span></span> | <span data-ttu-id="ee632-568">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-568">No</span></span> | <span data-ttu-id="ee632-569">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-569">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ee632-570">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="ee632-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="ee632-571">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-571">No</span></span> | <span data-ttu-id="ee632-572">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-572">Yes</span></span> | <span data-ttu-id="ee632-573">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-573">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ee632-574">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="ee632-574">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="ee632-575">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-575">No</span></span> | <span data-ttu-id="ee632-576">Nie</span><span class="sxs-lookup"><span data-stu-id="ee632-576">No</span></span> | <span data-ttu-id="ee632-577">Tak</span><span class="sxs-lookup"><span data-stu-id="ee632-577">Yes</span></span> |

<span data-ttu-id="ee632-578">Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="ee632-578">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="ee632-579">Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="ee632-579">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="ee632-580">`TryAdd{LIFETIME}` Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-580">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="ee632-581">W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="ee632-581">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="ee632-582">Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:</span><span class="sxs-lookup"><span data-stu-id="ee632-582">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="ee632-583">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="ee632-583">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="ee632-584">Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*.</span><span class="sxs-lookup"><span data-stu-id="ee632-584">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ee632-585">Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` .</span><span class="sxs-lookup"><span data-stu-id="ee632-585">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="ee632-586">Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany.</span><span class="sxs-lookup"><span data-stu-id="ee632-586">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="ee632-587">Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="ee632-587">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="ee632-588">W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` .</span><span class="sxs-lookup"><span data-stu-id="ee632-588">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="ee632-589">Drugi wiersz rejestruje `MyDep` `IMyDep2` .</span><span class="sxs-lookup"><span data-stu-id="ee632-589">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="ee632-590">Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="ee632-590">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ee632-591">Zachowanie iniekcji konstruktora</span><span class="sxs-lookup"><span data-stu-id="ee632-591">Constructor injection behavior</span></span>

<span data-ttu-id="ee632-592">Usługi mogą być rozwiązywane przez dwa mechanizmy:</span><span class="sxs-lookup"><span data-stu-id="ee632-592">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ee632-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="ee632-594">`ActivatorUtilities` jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="ee632-594">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="ee632-595">Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="ee632-595">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="ee632-596">Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .</span><span class="sxs-lookup"><span data-stu-id="ee632-596">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="ee632-597">Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="ee632-597">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="ee632-598">Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-598">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ee632-599">Konteksty Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ee632-599">Entity Framework contexts</span></span>

<span data-ttu-id="ee632-600">Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="ee632-600">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="ee632-601">Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="ee632-601">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="ee632-602">Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.</span><span class="sxs-lookup"><span data-stu-id="ee632-602">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ee632-603">Opcje okresu istnienia i rejestracji</span><span class="sxs-lookup"><span data-stu-id="ee632-603">Lifetime and registration options</span></span>

<span data-ttu-id="ee632-604">Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z unikatowym identyfikatorem `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="ee632-604">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="ee632-605">W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:</span><span class="sxs-lookup"><span data-stu-id="ee632-605">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ee632-606">Interfejsy są zaimplementowane w `Operation` klasie.</span><span class="sxs-lookup"><span data-stu-id="ee632-606">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="ee632-607">`Operation`Konstruktor generuje identyfikator GUID, jeśli nie został podany:</span><span class="sxs-lookup"><span data-stu-id="ee632-607">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="ee632-608">`OperationService`Zarejestrowano, który zależy od poszczególnych `Operation` typów.</span><span class="sxs-lookup"><span data-stu-id="ee632-608">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="ee632-609">Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.</span><span class="sxs-lookup"><span data-stu-id="ee632-609">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="ee632-610">Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` Usługa różni `IOperationTransient` się od `OperationId` `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="ee632-610">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="ee632-611">`OperationService` odbiera nowe wystąpienie `IOperationTransient` klasy.</span><span class="sxs-lookup"><span data-stu-id="ee632-611">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="ee632-612">Nowe wystąpienie daje inną wartość `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="ee632-612">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="ee632-613">Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` Usługa jest taka sama jak `OperationService` w ramach żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="ee632-613">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="ee632-614">W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.</span><span class="sxs-lookup"><span data-stu-id="ee632-614">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="ee632-615">Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-615">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="ee632-616">W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:</span><span class="sxs-lookup"><span data-stu-id="ee632-616">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="ee632-617">`IOperationSingletonInstance`Usługa używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty` .</span><span class="sxs-lookup"><span data-stu-id="ee632-617">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="ee632-618">Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).</span><span class="sxs-lookup"><span data-stu-id="ee632-618">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="ee632-619">Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi.</span><span class="sxs-lookup"><span data-stu-id="ee632-619">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="ee632-620">Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i `OperationService` .</span><span class="sxs-lookup"><span data-stu-id="ee632-620">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="ee632-621">Następnie na stronie zostaną wyświetlone wszystkie wartości klasy modelu strony i usługi `OperationId` za pomocą przypisań właściwości:</span><span class="sxs-lookup"><span data-stu-id="ee632-621">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="ee632-622">Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:</span><span class="sxs-lookup"><span data-stu-id="ee632-622">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="ee632-623">**Pierwsze żądanie:**</span><span class="sxs-lookup"><span data-stu-id="ee632-623">**First request:**</span></span>

<span data-ttu-id="ee632-624">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="ee632-624">Controller operations:</span></span>

<span data-ttu-id="ee632-625">Przejściowy: d233e165-f417-469B-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="ee632-625">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="ee632-626">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ee632-626">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ee632-627">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ee632-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ee632-628">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ee632-628">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ee632-629">`OperationService` składowa</span><span class="sxs-lookup"><span data-stu-id="ee632-629">`OperationService` operations:</span></span>

<span data-ttu-id="ee632-630">Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="ee632-630">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="ee632-631">Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ee632-631">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ee632-632">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ee632-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ee632-633">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ee632-633">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ee632-634">**Drugie żądanie:**</span><span class="sxs-lookup"><span data-stu-id="ee632-634">**Second request:**</span></span>

<span data-ttu-id="ee632-635">Operacje kontrolera:</span><span class="sxs-lookup"><span data-stu-id="ee632-635">Controller operations:</span></span>

<span data-ttu-id="ee632-636">Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="ee632-636">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="ee632-637">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ee632-637">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ee632-638">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ee632-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ee632-639">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ee632-639">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ee632-640">`OperationService` składowa</span><span class="sxs-lookup"><span data-stu-id="ee632-640">`OperationService` operations:</span></span>

<span data-ttu-id="ee632-641">Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="ee632-641">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="ee632-642">Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ee632-642">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ee632-643">Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ee632-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ee632-644">Wystąpienie: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ee632-644">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ee632-645">Zauważ, że `OperationId` wartości różnią się w zależności od żądania i między żądaniami:</span><span class="sxs-lookup"><span data-stu-id="ee632-645">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="ee632-646">Obiekty *przejściowe* są zawsze różne.</span><span class="sxs-lookup"><span data-stu-id="ee632-646">*Transient* objects are always different.</span></span> <span data-ttu-id="ee632-647">Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów.</span><span class="sxs-lookup"><span data-stu-id="ee632-647">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="ee632-648">Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.</span><span class="sxs-lookup"><span data-stu-id="ee632-648">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="ee632-649">Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.</span><span class="sxs-lookup"><span data-stu-id="ee632-649">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="ee632-650">*Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, czy `Operation` wystąpienie jest dostarczone w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee632-650">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="ee632-651">Wywoływanie usług z głównego</span><span class="sxs-lookup"><span data-stu-id="ee632-651">Call services from main</span></span>

<span data-ttu-id="ee632-652">Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-652">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="ee632-653">Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania.</span><span class="sxs-lookup"><span data-stu-id="ee632-653">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="ee632-654">Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="ee632-654">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="ee632-655">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="ee632-655">Scope validation</span></span>

<span data-ttu-id="ee632-656">Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="ee632-656">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ee632-657">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="ee632-657">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="ee632-658">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="ee632-658">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ee632-659">Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="ee632-659">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="ee632-660">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-660">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ee632-661">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="ee632-661">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ee632-662">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="ee632-662">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ee632-663">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="ee632-663">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ee632-664">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="ee632-664">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="ee632-665">Usługi żądania</span><span class="sxs-lookup"><span data-stu-id="ee632-665">Request Services</span></span>

<span data-ttu-id="ee632-666">Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="ee632-666">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="ee632-667">Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-667">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="ee632-668">Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .</span><span class="sxs-lookup"><span data-stu-id="ee632-668">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="ee632-669">Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="ee632-669">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="ee632-670">Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="ee632-670">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="ee632-671">To daje klasy, które są łatwiejsze do przetestowania.</span><span class="sxs-lookup"><span data-stu-id="ee632-671">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="ee632-672">Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="ee632-672">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ee632-673">Projektowanie usług do iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="ee632-673">Design services for dependency injection</span></span>

<span data-ttu-id="ee632-674">Najlepsze rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="ee632-674">Best practices are to:</span></span>

* <span data-ttu-id="ee632-675">Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-675">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="ee632-676">Unikaj stanowych, statycznych klas i elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="ee632-676">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ee632-677">Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.</span><span class="sxs-lookup"><span data-stu-id="ee632-677">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="ee632-678">Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-678">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ee632-679">Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-679">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="ee632-680">Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.</span><span class="sxs-lookup"><span data-stu-id="ee632-680">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ee632-681">Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="ee632-681">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ee632-682">Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy.</span><span class="sxs-lookup"><span data-stu-id="ee632-682">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="ee632-683">Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ee632-683">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="ee632-684">Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="ee632-684">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ee632-685">Usuwanie usług</span><span class="sxs-lookup"><span data-stu-id="ee632-685">Disposal of services</span></span>

<span data-ttu-id="ee632-686">Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="ee632-686">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="ee632-687">Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.</span><span class="sxs-lookup"><span data-stu-id="ee632-687">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="ee632-688">W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="ee632-688">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="ee632-689">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ee632-689">In the following example:</span></span>

* <span data-ttu-id="ee632-690">Wystąpienia usługi nie są tworzone przez kontener usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-690">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="ee632-691">Planowane okresy istnienia usług nie są znane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="ee632-691">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="ee632-692">Platforma nie usuwa automatycznie usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-692">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="ee632-693">Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ee632-693">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="ee632-694">Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych</span><span class="sxs-lookup"><span data-stu-id="ee632-694">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="ee632-695">Przejściowy, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-695">Transient, limited lifetime</span></span>

<span data-ttu-id="ee632-696">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="ee632-696">**Scenario**</span></span>

<span data-ttu-id="ee632-697">Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="ee632-697">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="ee632-698">Wystąpienie jest rozwiązane w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="ee632-698">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="ee632-699">Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.</span><span class="sxs-lookup"><span data-stu-id="ee632-699">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="ee632-700">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="ee632-700">**Solution**</span></span>

<span data-ttu-id="ee632-701">Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="ee632-701">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="ee632-702">W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego.</span><span class="sxs-lookup"><span data-stu-id="ee632-702">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="ee632-703">Jeśli typ końcowy ma inne zależności, fabryka może:</span><span class="sxs-lookup"><span data-stu-id="ee632-703">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="ee632-704">Odbierz <xref:System.IServiceProvider> w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="ee632-704">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="ee632-705">Użyj, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-705">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="ee632-706">Wystąpienie udostępnione, ograniczony okres istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-706">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="ee632-707">**Scenariusz**</span><span class="sxs-lookup"><span data-stu-id="ee632-707">**Scenario**</span></span>

<span data-ttu-id="ee632-708">Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> powinna mieć ograniczony okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="ee632-708">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="ee632-709">**Rozwiązanie**</span><span class="sxs-lookup"><span data-stu-id="ee632-709">**Solution**</span></span>

<span data-ttu-id="ee632-710">Zarejestruj wystąpienie z okresem istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="ee632-710">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="ee632-711">Użyj, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> Aby rozpocząć i utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="ee632-711">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="ee632-712">Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-712">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="ee632-713">Usuń zakres, gdy okres istnienia powinien zostać zakończony.</span><span class="sxs-lookup"><span data-stu-id="ee632-713">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="ee632-714">Ogólne wskazówki</span><span class="sxs-lookup"><span data-stu-id="ee632-714">General Guidelines</span></span>

* <span data-ttu-id="ee632-715">Nie rejestruj <xref:System.IDisposable> wystąpień z zakresem przejściowym.</span><span class="sxs-lookup"><span data-stu-id="ee632-715">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="ee632-716">Zamiast tego użyj wzorca fabryki.</span><span class="sxs-lookup"><span data-stu-id="ee632-716">Use the factory pattern instead.</span></span>
* <span data-ttu-id="ee632-717">Nie należy rozwiązywać wystąpień przejściowych lub zakresów <xref:System.IDisposable> w zakresie w zakresie głównym.</span><span class="sxs-lookup"><span data-stu-id="ee632-717">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="ee632-718">Jedyny ogólny wyjątek polega na tym, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> obiekt, który nie jest idealnym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="ee632-718">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="ee632-719">Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie.</span><span class="sxs-lookup"><span data-stu-id="ee632-719">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="ee632-720">Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.</span><span class="sxs-lookup"><span data-stu-id="ee632-720">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="ee632-721">Zakresy powinny służyć do kontrolowania okresów istnienia usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-721">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="ee632-722">Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.</span><span class="sxs-lookup"><span data-stu-id="ee632-722">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="ee632-723">Zastępowanie kontenera usług domyślnych</span><span class="sxs-lookup"><span data-stu-id="ee632-723">Default service container replacement</span></span>

<span data-ttu-id="ee632-724">Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich.</span><span class="sxs-lookup"><span data-stu-id="ee632-724">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="ee632-725">Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:</span><span class="sxs-lookup"><span data-stu-id="ee632-725">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="ee632-726">Iniekcja właściwości</span><span class="sxs-lookup"><span data-stu-id="ee632-726">Property injection</span></span>
* <span data-ttu-id="ee632-727">Iniekcja oparta na nazwie</span><span class="sxs-lookup"><span data-stu-id="ee632-727">Injection based on name</span></span>
* <span data-ttu-id="ee632-728">Kontenery podrzędne</span><span class="sxs-lookup"><span data-stu-id="ee632-728">Child containers</span></span>
* <span data-ttu-id="ee632-729">Niestandardowe zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="ee632-729">Custom lifetime management</span></span>
* <span data-ttu-id="ee632-730">`Func<T>` Obsługa inicjowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="ee632-730">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ee632-731">Rejestracja oparta na Konwencji</span><span class="sxs-lookup"><span data-stu-id="ee632-731">Convention-based registration</span></span>

<span data-ttu-id="ee632-732">Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:</span><span class="sxs-lookup"><span data-stu-id="ee632-732">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="ee632-733">Autofac</span><span class="sxs-lookup"><span data-stu-id="ee632-733">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="ee632-734">DryIoc</span><span class="sxs-lookup"><span data-stu-id="ee632-734">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="ee632-735">Prolongaty</span><span class="sxs-lookup"><span data-stu-id="ee632-735">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="ee632-736">LightInject</span><span class="sxs-lookup"><span data-stu-id="ee632-736">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="ee632-737">Lamar</span><span class="sxs-lookup"><span data-stu-id="ee632-737">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="ee632-738">Stashbox</span><span class="sxs-lookup"><span data-stu-id="ee632-738">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="ee632-739">Unity</span><span class="sxs-lookup"><span data-stu-id="ee632-739">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="ee632-740">Bezpieczeństwo wątkowe</span><span class="sxs-lookup"><span data-stu-id="ee632-740">Thread safety</span></span>

<span data-ttu-id="ee632-741">Twórz bezpieczne dla wątków usługi pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="ee632-741">Create thread-safe singleton services.</span></span> <span data-ttu-id="ee632-742">Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="ee632-742">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="ee632-743">Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo.</span><span class="sxs-lookup"><span data-stu-id="ee632-743">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ee632-744">Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.</span><span class="sxs-lookup"><span data-stu-id="ee632-744">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ee632-745">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="ee632-745">Recommendations</span></span>

* <span data-ttu-id="ee632-746">`async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="ee632-746">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="ee632-747">Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-747">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="ee632-748">Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-748">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="ee632-749">Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="ee632-749">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="ee632-750">Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ee632-750">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ee632-751">Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu.</span><span class="sxs-lookup"><span data-stu-id="ee632-751">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="ee632-752">Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="ee632-752">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="ee632-753">Unikaj statycznego dostępu do usług.</span><span class="sxs-lookup"><span data-stu-id="ee632-753">Avoid static access to services.</span></span> <span data-ttu-id="ee632-754">Na przykład należy unikać statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu.</span><span class="sxs-lookup"><span data-stu-id="ee632-754">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="ee632-755">Unikaj używania *wzorca lokalizatora usługi*, który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="ee632-755">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="ee632-756">Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:</span><span class="sxs-lookup"><span data-stu-id="ee632-756">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="ee632-757">**Niepoprawnie:**</span><span class="sxs-lookup"><span data-stu-id="ee632-757">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="ee632-758">**Poprawne**:</span><span class="sxs-lookup"><span data-stu-id="ee632-758">**Correct**:</span></span>

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

* <span data-ttu-id="ee632-759">Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie wykonywania za pomocą polecenia <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="ee632-759">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="ee632-760">Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="ee632-760">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="ee632-761">Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="ee632-761">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="ee632-762">Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.</span><span class="sxs-lookup"><span data-stu-id="ee632-762">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="ee632-763">DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global.</span><span class="sxs-lookup"><span data-stu-id="ee632-763">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="ee632-764">Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.</span><span class="sxs-lookup"><span data-stu-id="ee632-764">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee632-765">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ee632-765">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="ee632-766">Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee632-766">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="ee632-767">Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)</span><span class="sxs-lookup"><span data-stu-id="ee632-767">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="ee632-768">Zasada jawnych zależności</span><span class="sxs-lookup"><span data-stu-id="ee632-768">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="ee632-769">Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="ee632-769">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="ee632-770">Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="ee632-770">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
