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
ms.openlocfilehash: b0ba7c7598df13413c00934a30e03681129de98a
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227582"
---
# <a name="dependency-injection-in-aspnet-core"></a>Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)

::: moniker range=">= aspnetcore-3.0"

Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .

Aby uzyskać więcej informacji na temat wstrzykiwania zależności opcji, zobacz <xref:fundamentals/configuration/options> .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Przegląd iniekcji zależności

*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt. Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:

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

Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie. `MyDependency`Klasa jest zależnością `IndexModel` klasy:

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

Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia. Zależności kodu, takie jak w poprzednim przykładzie, są problematyczne i należy je uniknąć z następujących powodów:

* Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.
* Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę. W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.
* Ta implementacja jest trudna do testowania jednostkowego. Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.

Iniekcja zależności eliminuje te problemy w następujący sposób:

* Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.
* Rejestracja zależności w kontenerze usługi. ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> . Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.
* *Iniekcja* usługi do konstruktora klasy, w której jest używana. Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.

W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Ten interfejs jest implementowany przez konkretny typ `MyDependency` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` . <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>Metoda rejestruje usługę z okresem istnienia w określonym zakresie, okresem istnienia pojedynczego żądania. [Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

Ze wzorem DI:

* Kontroler nie używa konkretnego typu `MyDependency` , tylko interfejsu `IMyDependency` . Dzięki temu można łatwo zmienić implementację używaną przez kontroler bez modyfikowania kontrolera.
* Kontroler nie tworzy wystąpienia `MyDependency` , jest tworzone przez di kontener.

Implementację `IMyDependency` interfejsu można ulepszyć za pomocą wbudowanego interfejsu API rejestrowania:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

Zaktualizowana `ConfigureServices` Metoda rejestruje nową `IMyDependency` implementację:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2` żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze. Użycie iniekcji zależności w łańcuchu nie jest nietypowe. Każda żądana zależność z kolei żąda własnych zależności. Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi. Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.

`ILogger<TCategoryName>` to [Usługa udostępniona przez platformę](#framework-provided-services).

Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types).

W terminologii wtrysku zależności, usługa:

* Jest zazwyczaj obiektem, który dostarcza usługę do innego kodu w aplikacji, takiej jak `IMyDependency` Usługa.
* Nie jest powiązany z usługą sieci Web, chociaż usługa może korzystać z usługi sieci Web.

Struktura zapewnia niezawodny system [rejestrowania](xref:fundamentals/logging/index) . `IMyDependency`Implementacje zostały sporządzone w celu zaprezentowania podstawowej i nie zaimplementowania rejestrowania. Większość aplikacji nie musi być w trakcie pisania rejestratorów. Poniższy kod ilustruje użycie domyślnego rejestrowania, które nie wymaga zarejestrowania żadnych usług w programie `ConfigureServices` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

Korzystając z powyższego kodu, nie ma potrzeby aktualizowania, `ConfigureServices` ponieważ [Rejestrowanie](xref:fundamentals/logging/index) jest dostarczane przez strukturę:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a>Usługi wstrzykiwane do uruchamiania

Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Usługi można wstrzyknąć do `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup> i [Konfigurowanie dostępu podczas uruchamiania](xref:fundamentals/configuration/index#access-configuration-in-startup).

## <a name="register-additional-services-with-extension-methods"></a>Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających

Gdy jest dostępna metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi:

* Konwencją jest użycie pojedynczej `Add{SERVICE_NAME}` metody rozszerzenia w celu zarejestrowania wszystkich usług wymaganych przez tę usługę.
* Usługi zależne są również zarejestrowane.

Poniższy kod jest generowany przez Razor szablon stron przy użyciu poszczególnych kont użytkowników i pokazuje, jak dodać dodatkowe usługi do kontenera przy użyciu metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

Aby uzyskać więcej informacji, zobacz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> i <xref:security/authentication/identity>.

Zapoznaj się z sekcją [łączenie kolekcji usług](#csc) , aby uzyskać instrukcje dotyczące pisania metody rozszerzenia w celu zarejestrowania usług.

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi. Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:

### <a name="transient"></a>Administracyjnej

Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług. Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.

W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.

### <a name="scoped"></a>Zakresie

Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).

W przypadku korzystania z Entity Framework Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Metoda rozszerzenia rejestruje `DbContext` typy z okresem istnienia w zakresie domyślnie.

W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.

Korzystanie z usług objętych zakresem w oprogramowaniu pośredniczącym z jedną z następujących metod:

* Wsuń usługę do `Invoke` `InvokeAsync` metody lub. Wstrzyknięcie przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) zgłasza wyjątek w czasie wykonywania, ponieważ wymusza zachowanie usługi jako pojedynczej. Przykład w [opcjach okres istnienia i rejestracji](#lifetime-and-registration-options) używa `InvokeAsync` metody.
* [Oprogramowanie pośredniczące oparte na fabryce](<xref:fundamentals/middleware/extensibility>). <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> metody rozszerzające sprawdzają, czy typ zarejestrowanego oprogramowania pośredniczącego jest zaimplementowany <xref:Microsoft.AspNetCore.Http.IMiddleware> . W <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> takim przypadku wystąpienie zarejestrowane w kontenerze jest używane do rozpoznawania <xref:Microsoft.AspNetCore.Http.IMiddleware> implementacji zamiast korzystania z logiki aktywacji oprogramowania pośredniczącego opartego na Konwencji. Oprogramowanie pośredniczące jest rejestrowane jako usługa w zakresie lub przejściowym w kontenerze usługi aplikacji.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej. Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań. Warto:

* Rozwiązanie pojedynczej usługi z usługi w zakresie lub przejściowej.
* Rozwiąż usługę objętą zakresem z innej usługi w zakresie lub przejściowej.

Domyślnie w środowisku programistycznym rozpoznawanie usługi z innej usługi z dłuższym okresem istnienia zgłasza wyjątek. Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#sv).

### <a name="singleton"></a>Pojedynczego

Są tworzone pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ):

* Podczas pierwszego żądania.
* Przez dewelopera, gdy udostępnia wystąpienie implementacji bezpośrednio do kontenera. Takie podejście jest rzadko niezbędne.

Każde kolejne żądanie używa tego samego wystąpienia. Jeśli aplikacja wymaga pojedynczych zachowań, zezwól kontenerowi usługi na zarządzanie okresem istnienia usługi. Nie Wdrażaj wzorca projektu singleton i podawanie kodu do usuwania pojedynczych elementów. Usługi nigdy nie powinny być usuwane przez kod, który rozwiązał usługę z kontenera. Jeśli typ lub fabryka są zarejestrowane jako pojedyncze, kontener automatycznie usunie pojedyncze.

Usługi pojedyncze muszą być bezpieczne dla wątków i często używane w usługach bezstanowych.

W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji. Ponieważ pamięć nie jest wydawana do momentu wyłączenia aplikacji, należy rozważyć użycie pamięci z pojedynczą.

> [!WARNING]
> ***Nie*** należy rozwiązywać usługi z zakresem z pojedynczej. Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań. Rozwiązaniem jest rozwiązanie pojedynczej usługi z poziomu usługi w zakresie lub przejściowej.

## <a name="service-registration-methods"></a>Metody rejestracji usług

Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| Metoda | Automatyczny<br>object<br>myśl | Wiele<br>implementacje | Przekaż argumenty |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<IMyDep, MyDep>();` | Tak | Tak | Nie |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | Tak | Tak | Tak |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<MyDep>();` | Tak | Nie | Nie |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | Nie | Tak | Tak |
| `AddSingleton(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | Nie | Nie | Tak |

Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) . Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}` Metody rejestrują usługę, jeśli nie została jeszcze zarejestrowana implementacja.

W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` . Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Aby uzyskać więcej informacji, zobacz:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę, jeśli nie istnieje jeszcze implementacja tego *samego typu*. Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` . Podczas rejestrowania usług deweloper powinien dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany. Zazwyczaj autorzy biblioteki używają, `TryAddEnumerable` Aby uniknąć rejestrowania wielu kopii implementacji w kontenerze.

W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` . Drugi wiersz rejestruje `MyDep` `IMyDep2` . Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

Rejestracja usługi jest zazwyczaj kolejnością niezależną, z wyjątkiem rejestracji wielu implementacji tego samego typu.

`IServiceCollection` jest kolekcją <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> . Poniższy kod pokazuje, jak dodać usługę z konstruktorem:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

`Add{LIFETIME}`Metody te używają tego samego podejścia. Na przykład zapoznaj się z [kodem źródłowym i Addscoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).

### <a name="constructor-injection-behavior"></a>Zachowanie iniekcji konstruktora

Usługi mogą być rozwiązywane przez dwa mechanizmy:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:
  * Tworzy obiekty bez rejestracji usługi w kontenerze iniekcji zależności.
  * Używany z funkcjami platformy, takimi jak [pomocnicy tagów](xref:mvc/views/tag-helpers/intro), kontrolery MVC i [powiązania modeli](xref:mvc/models/model-binding).

Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.

Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .

Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor. Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.

## <a name="entity-framework-contexts"></a>Konteksty Entity Framework

Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta. Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych. Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.

## <a name="lifetime-and-registration-options"></a>Opcje okresu istnienia i rejestracji

Aby zademonstrować różnicę między opcjami okresu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z identyfikatorem `OperationId` . W zależności od sposobu skonfigurowania okresu istnienia usługi dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Interfejsy są zaimplementowane w `Operation` klasie. `Operation`Konstruktor generuje ostatnie 4 znaki identyfikatora GUID, jeśli jeden nie został podany:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach żądań i między nimi. Aplikacja Przykładowa `IndexModel` i oprogramowanie pośredniczące żąda każdego rodzaju `IOperation` typu i rejestruje `OperationId` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

Oprogramowanie pośredniczące jest podobne do `IndexModel` i rozwiązuje te same usługi:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

Usługi w zakresie muszą zostać rozwiązane w `InvokeAsync` metodzie:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

Dane wyjściowe rejestratora przedstawiają:

* Obiekty *przejściowe* są zawsze różne. Wartość przejściowa `OperationId` jest różna w oprogramowaniu i oprogramowaniu `IndexModel` pośredniczącym.
* Obiekty w *zakresie* są takie same w poszczególnych żądaniach, ale różnią się w zależności od każdego żądania.
* *Pojedyncze* obiekty są takie same dla każdego żądania.

Aby zmniejszyć ilość danych wyjściowych rejestrowania, ustaw wartość opcji "Rejestrowanie: LogLevel: Microsoft: Error" w *appsettings.Development.js* w pliku:

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>Wywoływanie usług z głównego

Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji. Takie podejście jest przydatne w celu uzyskania dostępu do usługi o określonym zakresie podczas uruchamiania, aby uruchomić zadania inicjowania:

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

Poprzedni kod pochodzi od [dodania inicjatora inicjatora](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) w Razor samouczku strony.

Poniższy przykład pokazuje, jak uzyskać kontekst dla `IMyDependency` `Program.Main` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>Weryfikacja zakresu

Gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments) i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do skompilowania hosta, domyślny dostawca usług sprawdza, czy:

* Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.
* Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.
* Usługi przejściowe nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych lub usług objętych zakresem.

Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany. Okres istnienia dostawcy usług głównych odpowiada okresowi istnienia aplikacji, gdy dostawca rozpoczyna pracę z aplikacją i jest usuwany po zamknięciu aplikacji.

Usługi o określonym zakresie są usuwane przez kontener, który go utworzył. Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja jest wyłączona. Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.

Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](xref:fundamentals/host/web-host#scope-validation).

## <a name="request-services"></a>Usługi żądania

Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .

Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji. Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .

Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio. Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól platformie na wstrzyknięcie zależności. To daje klasy, które są łatwiejsze do przetestowania.

ASP.NET Core tworzy zakres na żądanie i `RequestServices` uwidacznia dostawcę usług objętych zakresem. Wszystkie usługi objęte zakresem są prawidłowe przez cały czas, gdy żądanie jest aktywne.

> [!NOTE]
> Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.

## <a name="design-services-for-dependency-injection"></a>Projektowanie usług do iniekcji zależności

Najlepsze rozwiązania:

* Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.
* Unikaj stanowych, statycznych klas i elementów członkowskich. Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.
* Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług. Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.
* Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.

Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy. Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika.

### <a name="disposal-of-services"></a>Usuwanie usług

Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy. Usługi nigdy nie powinny być usuwane przez żaden kod, który rozwiązał usługę z kontenera. Jeśli typ lub fabryka są rejestrowane jako pojedyncze, kontener usuwa pojedyncze.

W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

Konsola debugowania pokazuje następujące dane wyjściowe po każdym odświeżeniu strony indeksu:

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>Usługi, które nie zostały utworzone przez kontener usługi
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
Spójrzmy na poniższy kod:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Wystąpienia usługi nie są tworzone przez kontener usługi.
* Planowane okresy istnienia usług nie są znane przez platformę.
* Platforma nie usuwa automatycznie usług.
* Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych

#### <a name="transient-limited-lifetime"></a>Przejściowy, ograniczony okres istnienia

**Scenariusz**

Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:

* Wystąpienie jest rozwiązane w zakresie głównym (kontener główny).
* Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.

**Rozwiązanie**

Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym. W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego. Jeśli typ końcowy ma inne zależności, fabryka może:

* Odbierz <xref:System.IServiceProvider> w konstruktorze.
* Użyj, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.

#### <a name="shared-instance-limited-lifetime"></a>Wystąpienie udostępnione, ograniczony okres istnienia

**Scenariusz**

Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> powinna mieć ograniczony okres istnienia.

**Rozwiązanie**

Zarejestruj wystąpienie z okresem istnienia w zakresie. Użyj, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> Aby rozpocząć i utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi. Usuń zakres, gdy okres istnienia powinien zostać zakończony.

#### <a name="general-idisposable-guidelines"></a>Ogólne wytyczne interfejsu IDisposable

* Nie rejestruj <xref:System.IDisposable> wystąpień z zakresem przejściowym. Zamiast tego użyj wzorca fabryki.
* Nie należy rozwiązywać wystąpień przejściowych lub zakresów <xref:System.IDisposable> w zakresie w zakresie głównym. Jedyny ogólny wyjątek polega na tym, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> obiekt, który nie jest idealnym wzorcem.
* Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie. Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.
* Zakresy powinny służyć do kontrolowania okresów istnienia usług. Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.

## <a name="default-service-container-replacement"></a>Zastępowanie kontenera usług domyślnych

Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich. Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:

* Iniekcja właściwości
* Iniekcja oparta na nazwie
* Kontenery podrzędne
* Niestandardowe zarządzanie okresem istnienia
* `Func<T>` Obsługa inicjowania z opóźnieniem
* Rejestracja oparta na Konwencji

Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Prolongaty](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpieczeństwo wątkowe

Twórz bezpieczne dla wątków usługi pojedyncze. Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.

Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo. Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.

## <a name="recommendations"></a>Zalecenia

* `async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane. Język C# nie obsługuje konstruktorów asynchronicznych. Zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.
* Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi. Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi. Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options). Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu. Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.
* Unikaj statycznego dostępu do usług. Na przykład należy unikać statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu).
* Utrzymuj szybkie i synchroniczne operacje.
* Unikaj używania *wzorca lokalizatora usługi*. Na przykład nie wywołuj, <xref:System.IServiceProvider.GetService*> Aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:

  **Niepoprawnie:**

    ![Niepoprawny kod](dependency-injection/_static/bad.png)

  **Poprawne**:

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
* Inna odmiana lokalizatora usługi, aby uniknąć, wprowadza fabrykę, która rozwiązuje zależności w czasie wykonywania. Obie te praktyki mieszają się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .
* Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

<a name="ASP0000"></a>
* Należy unikać wywołań do <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> programu `ConfigureServices` . Wywoływanie `BuildServiceProvider` zazwyczaj odbywa się, gdy deweloper chce rozwiązać usługę w programie `ConfigureServices` . Rozważmy na przykład przypadek, w którym musisz się zapoznać `LoginPath` z konfiguracją. Unikaj następującego kodu:

  ![zły kod wywołujący BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  Na powyższym obrazie, wybierając zieloną linię falistą w obszarze `services.BuildServiceProvider` pokazuje następujące ostrzeżenie ASP0000:
    * ASP0000 wywołanie "BuildServiceProvider" z kodu aplikacji spowoduje utworzenie dodatkowej kopii pojedynczych usług. Należy rozważyć alternatywy, takie jak "wstrzyknięcie usług" jako parametry do "configure".

   Wywołanie `BuildServiceProvider` tworzy drugi kontener, który może tworzyć niezerwane pojedyncze i spowodować odwołania do grafów obiektów w wielu kontenerach. Prawidłowym sposobem uzyskania `LoginPath` jest użycie wzorca opcji z opcją di:

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* Nierozporządzalne usługi przejściowe są przechwytywane przez kontener do usuwania. Może to obsłużyć przeciek pamięci, jeśli został rozwiązany z kontenera najwyższego poziomu.
* Włącz weryfikację zakresu, aby upewnić się, że aplikacja nie ma usług objętych zakresem przechwytujących pojedyncze. Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).

Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane. Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.

DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global. Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Zalecane wzorce dla wielu dzierżawców w programie DI

[Podstawowe funkcje sadu](https://github.com/OrchardCMS/OrchardCore) zapewniają wiele dzierżawców. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją podstawową programu sadu](https://docs.orchardcore.net/en/dev/).

Zapoznaj się z https://github.com/OrchardCMS/OrchardCore.Samples przykładowymi aplikacjami, aby poznać Przykłady sposobu tworzenia aplikacji modularnych i wielodostępnych przy użyciu samej platformy sadu Core bez żadnej z określonych funkcji usługi CMS.

## <a name="framework-provided-services"></a>Usługi udostępniane przez platformę

`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC. Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host). Aplikacje oparte na szablonach ASP.NET Core mają więcej niż 250 usług zarejestrowanych przez platformę. W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.

| Typ usługi | Okres istnienia |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Pojedynczego |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Pojedynczego |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Pojedynczego |

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [Wzorce konferencji NDC na potrzeby tworzenia aplikacji](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Zasada jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)](https://www.martinfowler.com/articles/injection.html)
* [Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Steve Kowalski](https://ardalis.com/), [Scott Addie](https://scottaddie.com)i [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności, który jest techniką do osiągnięcia [niewersji kontroli (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

Aby uzyskać więcej informacji specyficznych dla iniekcji zależności w kontrolerach MVC, zobacz <xref:mvc/controllers/dependency-injection> .

[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Przegląd iniekcji zależności

*Zależność* jest dowolnym obiektem, który jest wymagany przez inny obiekt. Zapoznaj się `MyDependency` z następującą klasą, korzystając z `WriteMessage` metody, na której zależą inne klasy w aplikacji:

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

Wystąpienie `MyDependency` klasy można utworzyć w celu udostępnienia `WriteMessage` metody klasie. `MyDependency`Klasa jest zależnością `IndexModel` klasy:

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

Klasa tworzy i bezpośrednio zależy od `MyDependency` wystąpienia. Zależności kodu (takie jak w poprzednim przykładzie) są problematyczne i należy je unikać z następujących powodów:

* Aby zastąpić `MyDependency` inną implementacją, należy zmodyfikować klasę.
* Jeśli `MyDependency` ma zależności, muszą one być skonfigurowane przez klasę. W dużym projekcie z wieloma klasami, w zależności od tego `MyDependency` , kod konfiguracji jest rozmieszczany w całej aplikacji.
* Ta implementacja jest trudna do testowania jednostkowego. Aplikacja powinna używać klasy imitacji lub zastępczej `MyDependency` , która nie jest możliwa w przypadku tego podejścia.

Iniekcja zależności eliminuje te problemy w następujący sposób:

* Użycie interfejsu lub klasy bazowej do abstrakcyjnej implementacji zależności.
* Rejestracja zależności w kontenerze usługi. ASP.NET Core udostępnia wbudowany kontener usługi, <xref:System.IServiceProvider> . Usługi są zarejestrowane w `Startup.ConfigureServices` metodzie aplikacji.
* *Iniekcja* usługi do konstruktora klasy, w której jest używana. Struktura przejmuje odpowiedzialność za utworzenie wystąpienia zależności i jego likwidację, gdy nie jest już potrzebny.

W [przykładowej aplikacji](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` Interfejs definiuje metodę dostarczaną przez usługę do aplikacji:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Ten interfejs jest implementowany przez konkretny typ `MyDependency` :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w konstruktorze. Użycie iniekcji zależności w łańcuchu nie jest nietypowe. Każda żądana zależność z kolei żąda własnych zależności. Kontener rozwiązuje zależności w grafie i zwraca w pełni rozwiązane usługi. Zestaw zbiorczy zależności, które muszą zostać rozwiązane, jest zwykle nazywany *drzewem zależności*, *wykresem zależności*lub *wykresem obiektów*.

`IMyDependency` i `ILogger<TCategoryName>` musi być zarejestrowany w kontenerze usługi. `IMyDependency` jest zarejestrowany w `Startup.ConfigureServices` . `ILogger<TCategoryName>` jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [Usługa udostępniona przez platformę](#framework-provided-services) zarejestrowana domyślnie przez platformę.

Kontener jest rozpoznawany `ILogger<TCategoryName>` przez wykorzystanie [(rodzajowe) otwartych typów](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminując konieczność zarejestrowania każdego [(rodzajowego) konstruowanego typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

W przykładowej aplikacji `IMyDependency` Usługa jest zarejestrowana w konkretnym typie `MyDependency` . Rejestracja zakresów okresu istnienia usługi do okresu istnienia pojedynczego żądania. [Okresy istnienia usługi](#service-lifetimes) zostały opisane w dalszej części tego tematu.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Każda `services.Add{SERVICE_NAME}` Metoda rozszerzenia dodaje usługi (i potencjalnie konfiguruje). Na przykład `services.AddMvc()` dodaje Razor strony usług i wymagane przez MVC. Zalecamy, aby aplikacje były zgodne z tą konwencją. Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.

Jeśli Konstruktor usługi wymaga [wbudowanego typu](/dotnet/csharp/language-reference/keywords/built-in-types-table), takiego jak `string` ,, typ można wstrzyknąć przy użyciu [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji](xref:fundamentals/configuration/options):

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

Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której jest używana usługa i jest przypisywana do pola prywatnego. To pole jest używane w celu uzyskania dostępu do usługi w zależności od potrzeb w całej klasie.

W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane do wywołania `WriteMessage` metody usługi:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Usługi wstrzykiwane do uruchamiania

Tylko następujące typy usług można wstrzyknąć do `Startup` konstruktora, gdy jest używany host generyczny ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Usługi można wstrzyknąć do `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Usługi udostępniane przez platformę

`Startup.ConfigureServices`Metoda jest odpowiedzialna za Definiowanie usług, z których korzysta aplikacja, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC. Początkowo `IServiceCollection` dostarczone z `ConfigureServices` usługami zdefiniowanymi przez platformę, w zależności od [konfiguracji hosta](xref:fundamentals/index#host). Aplikacja oparta na ASP.NET Core szablonie nie jest nietypowa, aby mieć setki usług zarejestrowanych przez platformę. W poniższej tabeli wymieniono niewielki przykład usług zarejestrowanych w ramach platformy.

| Typ usługi | Okres istnienia |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Pojedynczego |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Administracyjnej |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Pojedynczego |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Pojedynczego |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Pojedynczego |

## <a name="register-additional-services-with-extension-methods"></a>Rejestrowanie dodatkowych usług przy użyciu metod rozszerzających

Gdy dostępna jest Metoda rozszerzenia kolekcji usług w celu zarejestrowania usługi (i zależnych od niej usług, jeśli jest to wymagane), Konwencja ma używać pojedynczej `Add{SERVICE_NAME}` metody rozszerzającej do rejestrowania wszystkich usług wymaganych przez tę usługę. Poniższy kod stanowi przykład dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzających [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :

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

Aby uzyskać więcej informacji, zapoznaj się z <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> klasą w dokumentacji interfejsu API.

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi. Usługi ASP.NET Core można skonfigurować przy użyciu następujących okresów istnienia:

### <a name="transient"></a>Administracyjnej

Przejściowe usługi okresu istnienia <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> są tworzone za każdym razem, gdy zażądają one kontenera usług. Ten okres istnienia działa najlepiej w przypadku lekkich i bezstanowych usług.

W przypadku aplikacji, które przetwarzają żądania, usługi przejściowe są usuwane na końcu żądania.

### <a name="scoped"></a>Zakresie

Usługi okresu istnienia w zakresie ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) są tworzone raz dla każdego żądania klienta (połączenie).

W przypadku aplikacji, które przetwarzają żądania, usługi o określonym zakresie są usuwane na końcu żądania.

> [!WARNING]
> W przypadku korzystania z usługi w zakresie w oprogramowaniu pośredniczącym należy wstrzyknąć usługę do `Invoke` `InvokeAsync` metody lub. Nie wprowadzaj przez [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , ponieważ wymusza ona zachowanie usługi jako pojedynczej. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Pojedynczego

Pojedyncze usługi okresu istnienia ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) są tworzone podczas pierwszego żądania (lub gdy `Startup.ConfigureServices` jest uruchamiany, a wystąpienie jest określone przy rejestracji usługi). Każde kolejne żądanie używa tego samego wystąpienia. Jeśli aplikacja wymaga pojedynczych zachowań, zaleca się, aby można było zarządzać okresem istnienia usługi przez kontener usługi. Nie Wdrażaj wzorca projektu singleton i podaj kod użytkownika, aby zarządzać okresem istnienia obiektu w klasie.

W przypadku aplikacji, które przetwarzają żądania, pojedyncze usługi są usuwane, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> zostanie usunięty podczas zamykania aplikacji.

> [!WARNING]
> Rozwiązanie usługi o określonym zakresie z pojedynczej jest niebezpieczne. Może to spowodować, że usługa będzie mieć nieprawidłowy stan podczas przetwarzania kolejnych żądań.

## <a name="service-registration-methods"></a>Metody rejestracji usług

Metody rozszerzenia rejestracji usług oferują przeciążenia, które są przydatne w określonych scenariuszach.

| Metoda | Automatyczny<br>object<br>myśl | Wiele<br>implementacje | Przekaż argumenty |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<IMyDep, MyDep>();` | Tak | Tak | Nie |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Tak | Tak | Tak |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<MyDep>();` | Tak | Nie | Nie |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Nie | Tak | Tak |
| `AddSingleton(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Nie | Nie | Tak |

Aby uzyskać więcej informacji na temat usuwania typów, zobacz sekcję [dotyczącą usuwania usług](#disposal-of-services) . Typowym scenariuszem dla wielu implementacji jest [imitacja typów do testowania](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}` Metody rejestrują usługę tylko wtedy, gdy nie zarejestrowano jeszcze implementacji.

W poniższym przykładzie pierwszy wiersz rejestruje `MyDependency` `IMyDependency` . Drugi wiersz nie ma wpływu, ponieważ `IMyDependency` ma już zarejestrowaną implementację:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Aby uzyskać więcej informacji, zobacz:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Metody [TryAddEnumerable (servicedescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie istnieje jeszcze implementacja tego *samego typu*. Wiele usług jest rozpoznawanych za pośrednictwem `IEnumerable<{SERVICE}>` . Podczas rejestrowania usług deweloper chce tylko dodać wystąpienie, jeśli jeden z tych samych typów nie został jeszcze dodany. Ogólnie rzecz biorąc, ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.

W poniższym przykładzie pierwszy wiersz rejestruje `MyDep` `IMyDep1` . Drugi wiersz rejestruje `MyDep` `IMyDep2` . Trzeci wiersz nie działa, ponieważ `IMyDep1` ma już zarejestrowana implementacja `MyDep` :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Zachowanie iniekcji konstruktora

Usługi mogą być rozwiązywane przez dwa mechanizmy:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności. `ActivatorUtilities` jest używany z abstrakcjami dostępnymi dla użytkowników, takimi jak pomocnicy tagów, kontrolery MVC i powiązania modelu.

Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.

Gdy usługi są rozwiązane przez `IServiceProvider` lub `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego* .

Gdy usługi są rozwiązane przez `ActivatorUtilities` , [iniekcja konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, aby istnieje tylko jeden odpowiedni Konstruktor. Przeciążenia konstruktora są obsługiwane, ale może istnieć tylko jedno Przeciążenie, którego argumenty mogą być spełnione przez iniekcję zależności.

## <a name="entity-framework-contexts"></a>Konteksty Entity Framework

Konteksty Entity Framework są zazwyczaj dodawane do kontenera usługi przy użyciu [okresu istnienia zakresu](#service-lifetimes) , ponieważ operacje bazy danych aplikacji sieci Web są zwykle ograniczone do żądania klienta. Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie zostanie określony przez Przeciążenie [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) podczas rejestrowania kontekstu bazy danych. Usługi danego okresu istnienia nie powinny używać kontekstu bazy danych z krótszym okresem istnienia niż usługa.

## <a name="lifetime-and-registration-options"></a>Opcje okresu istnienia i rejestracji

Aby zademonstrować różnicę między opcjami czasu istnienia i rejestracji, należy wziąć pod uwagę następujące interfejsy, które reprezentują zadania jako operacje z unikatowym identyfikatorem `OperationId` . W zależności od sposobu skonfigurowania okresu istnienia usługi operacji dla następujących interfejsów kontener zawiera to samo lub inne wystąpienie usługi, gdy żądanie klasy:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Interfejsy są zaimplementowane w `Operation` klasie. `Operation`Konstruktor generuje identyfikator GUID, jeśli nie został podany:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService`Zarejestrowano, który zależy od poszczególnych `Operation` typów. Gdy `OperationService` żądanie jest wykonywane za pośrednictwem iniekcji zależności, otrzymuje nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.

* Gdy usługi przejściowe są tworzone po zażądaniu kontenera, `OperationId` Usługa różni `IOperationTransient` się od `OperationId` `OperationService` . `OperationService` odbiera nowe wystąpienie `IOperationTransient` klasy. Nowe wystąpienie daje inną wartość `OperationId` .
* Gdy usługi w zakresie są tworzone dla każdego żądania klienta, `OperationId` `IOperationScoped` Usługa jest taka sama jak `OperationService` w ramach żądania klienta. W przypadku żądań klientów obie te usługi współdzielą inną `OperationId` wartość.
* Gdy pojedyncze i pojedyncze usługi wystąpienia są tworzone raz i używane przez wszystkie żądania klientów i wszystkie usługi, `OperationId` jest to stała między wszystkimi żądaniami obsługi.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

W programie `Startup.ConfigureServices` każdy typ jest dodawany do kontenera zgodnie z jego nazwanym okresem istnienia:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance`Usługa używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty` . Jest to jasne, gdy ten typ jest używany (jego identyfikator GUID to wszystkie zera).

Przykładowa aplikacja pokazuje okresy istnienia obiektów w ramach poszczególnych żądań i między nimi. Przykładowa aplikacja `IndexModel` wysyła żądania każdego rodzaju `IOperation` typu i `OperationService` . Następnie na stronie zostaną wyświetlone wszystkie wartości klasy modelu strony i usługi `OperationId` za pomocą przypisań właściwości:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Dwa następujące dane wyjściowe pokazują wyniki dwóch żądań:

**Pierwsze żądanie:**

Operacje kontrolera:

Przejściowy: d233e165-f417-469B-A866-1cf1935d2518  
Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000

`OperationService` składowa

Przejściowy: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000

**Drugie żądanie:**

Operacje kontrolera:

Przejściowy: b63bd538-0a37-4FF1-90ba-081c5138dda0  
Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000

`OperationService` składowa

Przejściowy: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Pojedyncze: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000

Zauważ, że `OperationId` wartości różnią się w zależności od żądania i między żądaniami:

* Obiekty *przejściowe* są zawsze różne. Wartość przejściowa `OperationId` dla pierwszego i drugiego żądania klienta różni się w zależności od `OperationService` operacji i między żądaniami klientów. Nowe wystąpienie jest dostarczane do każdego żądania obsługi i żądania klienta.
* Obiekty w *zakresie* są takie same w obrębie żądania klienta, ale różnią się w zależności od żądań klientów.
* *Pojedyncze* obiekty są takie same dla każdego obiektu i każdego żądania, niezależnie od tego, czy `Operation` wystąpienie jest dostarczone w `Startup.ConfigureServices` .

## <a name="call-services-from-main"></a>Wywoływanie usług z głównego

Utwórz element <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory. isscope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) , aby rozwiązać usługę objętą zakresem w zakresie aplikacji. Takie podejście jest przydatne do uzyskiwania dostępu do usługi w zakresie podczas uruchamiania do uruchamiania zadań inicjowania. Poniższy przykład pokazuje, jak uzyskać kontekst dla `MyScopedService` `Program.Main` :

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

## <a name="scope-validation"></a>Weryfikacja zakresu

Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług sprawdza, czy:

* Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.
* Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.

Dostawca usług głównych jest tworzony, gdy <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> jest wywoływany. Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.

Usługi o określonym zakresie są usuwane przez kontener, który go utworzył. Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony. Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.   

## <a name="request-services"></a>Usługi żądania

Usługi dostępne w ramach żądania ASP.NET Core `HttpContext` są udostępniane za pośrednictwem kolekcji [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .

Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji. Gdy obiekty określają zależności, są one spełnione przez typy Znalezione w `RequestServices` , nie `ApplicationServices` .

Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio. Zamiast tego Zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas, i zezwól na wstrzyknięcie zależności przez strukturę. To daje klasy, które są łatwiejsze do przetestowania.

> [!NOTE]
> Preferuj żądania zależności jako parametry konstruktora, aby uzyskać dostęp do `RequestServices` kolekcji.

## <a name="design-services-for-dependency-injection"></a>Projektowanie usług do iniekcji zależności

Najlepsze rozwiązania:

* Projektowanie usług do korzystania z iniekcji zależności w celu uzyskania ich zależności.
* Unikaj stanowych, statycznych klas i elementów członkowskich. Zaprojektuj aplikacje do korzystania z pojedynczych usług, aby uniknąć tworzenia stanu globalnego.
* Unikaj bezpośredniego tworzenia wystąpień klas zależnych w ramach usług. Bezpośrednie utworzenie wystąpienia Couples kod do konkretnej implementacji.
* Twórz klasy aplikacji małymi, dobrze i łatwo przetestowane.

Jeśli Klasa prawdopodobnie ma zbyt wiele zawstrzykiwanych zależności, zwykle jest to znak, że Klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Próba refaktoryzacji klasy przez przeniesienie niektórych jej obowiązków do nowej klasy. Należy pamiętać, że klasy Razor klas modelu stron i kontrolery MVC powinny skupić się na problemach z interfejsem użytkownika. Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [oddzielnych obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Usuwanie usług

Kontener wywołuje <xref:System.IDisposable.Dispose*> <xref:System.IDisposable> typy, które tworzy. Jeśli wystąpienie jest dodawane do kontenera przez kod użytkownika, nie zostanie usunięte automatycznie.

W poniższym przykładzie usługi są tworzone przez kontener usługi i usuwane automatycznie:

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

W poniższym przykładzie:

* Wystąpienia usługi nie są tworzone przez kontener usługi.
* Planowane okresy istnienia usług nie są znane przez platformę.
* Platforma nie usuwa automatycznie usług.
* Jeśli usługi nie zostały jawnie usunięte w kodzie dewelopera, są zachowywane do momentu zamknięcia aplikacji.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Wskazówki interfejsu IDisposable dla wystąpień przejściowych i współużytkowanych

#### <a name="transient-limited-lifetime"></a>Przejściowy, ograniczony okres istnienia

**Scenariusz**

Aplikacja wymaga <xref:System.IDisposable> wystąpienia z przejściowym okresem istnienia dla jednego z następujących scenariuszy:

* Wystąpienie jest rozwiązane w zakresie głównym.
* Wystąpienie powinno zostać usunięte przed zakończeniem zakresu.

**Rozwiązanie**

Użyj wzorca fabryki, aby utworzyć wystąpienie poza zakresem nadrzędnym. W tej sytuacji aplikacja zwykle ma `Create` metodę, która wywołuje bezpośrednio Konstruktor typu końcowego. Jeśli typ końcowy ma inne zależności, fabryka może:

* Odbierz <xref:System.IServiceProvider> w konstruktorze.
* Użyj, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> Aby utworzyć wystąpienie wystąpienia poza kontenerem, przy użyciu kontenera dla jego zależności.

#### <a name="shared-instance-limited-lifetime"></a>Wystąpienie udostępnione, ograniczony okres istnienia

**Scenariusz**

Aplikacja wymaga <xref:System.IDisposable> wystąpienia udostępnionego w wielu usługach, ale <xref:System.IDisposable> powinna mieć ograniczony okres istnienia.

**Rozwiązanie**

Zarejestruj wystąpienie z okresem istnienia w zakresie. Użyj, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> Aby rozpocząć i utworzyć nowy <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . Użyj zakresu, <xref:System.IServiceProvider> Aby uzyskać wymagane usługi. Usuń zakres, gdy okres istnienia powinien zostać zakończony.

#### <a name="general-guidelines"></a>Ogólne wskazówki

* Nie rejestruj <xref:System.IDisposable> wystąpień z zakresem przejściowym. Zamiast tego użyj wzorca fabryki.
* Nie należy rozwiązywać wystąpień przejściowych lub zakresów <xref:System.IDisposable> w zakresie w zakresie głównym. Jedyny ogólny wyjątek polega na tym, że aplikacja tworzy/odtworzy i usuwa <xref:System.IServiceProvider> obiekt, który nie jest idealnym wzorcem.
* Odebranie <xref:System.IDisposable> zależności za pośrednictwem programu di nie wymaga, aby odbiorca zaimplementował <xref:System.IDisposable> sam siebie. Odbiorca zależności nie <xref:System.IDisposable> powinien wywoływać <xref:System.IDisposable.Dispose%2A> tej zależności.
* Zakresy powinny służyć do kontrolowania okresów istnienia usług. Zakresy nie są hierarchiczne i nie ma żadnych specjalnych połączeń między zakresami.

## <a name="default-service-container-replacement"></a>Zastępowanie kontenera usług domyślnych

Wbudowany kontener usług został zaprojektowany z myślą o potrzebach platformy i większości aplikacji konsumenckich. Zalecamy użycie wbudowanego kontenera, chyba że potrzebna jest konkretna funkcja, która nie jest obsługiwana przez wbudowany kontener, na przykład:

* Iniekcja właściwości
* Iniekcja oparta na nazwie
* Kontenery podrzędne
* Niestandardowe zarządzanie okresem istnienia
* `Func<T>` Obsługa inicjowania z opóźnieniem
* Rejestracja oparta na Konwencji

Za pomocą aplikacji ASP.NET Core można używać następujących kontenerów innych firm:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Prolongaty](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpieczeństwo wątkowe

Twórz bezpieczne dla wątków usługi pojedyncze. Jeśli usługa singleton ma zależność od przejściowej usługi, usługa przejściowa może również wymagać bezpieczeństwa wątku, w zależności od tego, w jaki sposób jest używana przez pojedyncze.

Metoda fabryki pojedynczej usługi, taka jak drugi argument funkcji [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczna wątkowo. Podobnie jak w przypadku `static` konstruktora typu (), gwarantowane jest wywoływanie jednokrotne przez pojedynczy wątek.

## <a name="recommendations"></a>Zalecenia

* `async/await``Task`rozpoznawanie usług na podstawie nie jest obsługiwane. Język C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec polega na użyciu metod asynchronicznych po synchronicznym rozpoznaniu usługi.

* Unikaj przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi. Na przykład koszyk użytkownika nie powinien być zazwyczaj dodawany do kontenera usługi. Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options). Podobnie należy unikać obiektów "posiadaczy danych", które istnieją tylko w celu zezwolenia na dostęp do innego obiektu. Lepiej jest zażądać rzeczywistego elementu za pośrednictwem DI.
* Unikaj statycznego dostępu do usług. Na przykład należy unikać statycznego wpisywania [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użytku w innym miejscu.

* Unikaj używania *wzorca lokalizatora usługi*, który miesza się [z niewersjami strategii kontroli](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .
  * Nie wywołuj <xref:System.IServiceProvider.GetService*> , aby uzyskać wystąpienie usługi, gdy można użyć di zamiast:

    **Niepoprawnie:**

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
   
    **Poprawne**:

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

* Unikaj wstrzykiwania fabryki, która rozwiązuje zależności w czasie wykonywania za pomocą polecenia <xref:System.IServiceProvider.GetService*> .
* Unikaj dostępu statycznego do `HttpContext` (na przykład [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Podobnie jak w przypadku wszystkich zestawów zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane. Wyjątki są rzadkimi, głównie szczególnymi przypadkami w ramach samej struktury.

DI jest *alternatywą* dla wzorców dostępu do obiektów static/Global. Możesz nie być w stanie korzystać z zalet programu DI w przypadku jego mieszania z dostępem do obiektów statycznych.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Cztery sposoby usuwania interfejsu IDisposable w ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Pisanie czystego kodu w ASP.NET Core z iniekcją zależności (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Zasada jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Niewersja kontenerów sterowania i wzorzec iniekcji zależności (Martin Fowlera)](https://www.martinfowler.com/articles/injection.html)
* [Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
