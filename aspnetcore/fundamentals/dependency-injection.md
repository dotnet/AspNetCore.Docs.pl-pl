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
# <a name="dependency-injection-in-aspnet-core"></a>Dependency injection in ASP.NET Core (Wstrzykiwanie zależności na platformie ASP.NET Core)

Przez [Steve Smith](https://ardalis.com/) i Scott [Addie](https://scottaddie.com)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core obsługuje wzorzec projektowania oprogramowania wtrysku zależności (DI), który jest techniką osiągnięcia [inwersji kontroli (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

Aby uzyskać więcej informacji dotyczących iniekcji <xref:mvc/controllers/dependency-injection>zależności w kontrolerach MVC, zobacz .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Omówienie iniekcji zależności

*Zależność* jest dowolny obiekt, który wymaga innego obiektu. Sprawdź następującą `MyDependency` klasę `WriteMessage` za pomocą metody, od których zależą inne klasy w aplikacji:

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

Wystąpienie klasy `MyDependency` można utworzyć, aby `WriteMessage` udostępnić metodę do klasy. Klasa `MyDependency` jest zależnością `IndexModel` klasy:

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

Klasa tworzy i bezpośrednio zależy `MyDependency` od wystąpienia. Zależności kodu (takie jak poprzedni przykład) są problematyczne i należy ich unikać z następujących powodów:

* Aby `MyDependency` zastąpić inną implementację, klasa musi zostać zmodyfikowana.
* Jeśli `MyDependency` ma zależności, muszą być skonfigurowane przez klasę. W dużym projekcie z wieloma `MyDependency`klasami w zależności od kodu konfiguracji staje się rozproszone w całej aplikacji.
* Ta implementacja jest trudne do jednostkowego testu. Aplikacja powinna używać klasy `MyDependency` makiety lub skrótowej, co nie jest możliwe w przypadku tego podejścia.

Iniekcja zależności rozwiązuje następujące problemy poprzez:

* Użycie interfejsu lub klasy podstawowej do abstrakcyjnej implementacji zależności.
* Rejestracja zależności w kontenerze usługi. ASP.NET Core zapewnia wbudowany kontener serwisowy, <xref:System.IServiceProvider>. Usługi są rejestrowane w `Startup.ConfigureServices` metodzie aplikacji.
* *Wtrysk* usługi do konstruktora klasy, w której jest używana. Struktura bierze na siebie odpowiedzialność tworzenia wystąpienia zależności i usuwania go, gdy nie jest już potrzebne.

W [przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)aplikacji `IMyDependency` interfejs definiuje metodę, którą usługa zapewnia aplikacji:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Ten interfejs jest realizowany przez `MyDependency`konkretny typ:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w jego konstruktorze. Nie jest niczym niezwykłym, aby użyć iniekcji zależności w sposób łańcuchowy. Każda żądana zależność z kolei żąda własnych zależności. Kontener rozpoznaje zależności na wykresie i zwraca w pełni rozwiązaną usługę. Zbiorczy zestaw zależności, które muszą zostać rozwiązane, jest zazwyczaj określany jako *drzewo zależności,* *wykres zależności*lub *wykres obiektu.*

`IMyDependency`i `ILogger<TCategoryName>` muszą być zarejestrowane w kontenerze serwisowym. `IMyDependency`jest zarejestrowana w `Startup.ConfigureServices`. `ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [usługa świadczona przez framework](#framework-provided-services) zarejestrowana domyślnie przez platformę.

Kontener jest `ILogger<TCategoryName>` rozwiązywany, korzystając z [(ogólnych) otwartych typów,](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)eliminując konieczność rejestrowania każdego [(ogólnego) typu skonstruowanego:](/dotnet/csharp/language-reference/language-specification/types#constructed-types)

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana `MyDependency`z typem betonu . Zakresy rejestracji okres istnienia usługi do okresu istnienia pojedynczego żądania. [Okresy istnienia usługi](#service-lifetimes) są opisane w dalszej części tego tematu.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Każda `services.Add{SERVICE_NAME}` metoda rozszerzenia dodaje (i potencjalnie konfiguruje) usługi. Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC wymagają. Zaleca się, aby aplikacje postępowali zgodnie z tą konwencją. Umieść metody rozszerzenia w obszarze nazw [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) aby hermetyzować grupy rejestracji usług.

Jeśli konstruktor usługi wymaga [wbudowanego typu,](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`takiego jak , typ może zostać wstrzyknięty za pomocą [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji:](xref:fundamentals/configuration/options)

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

Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której usługa jest używana i przypisana do pola prywatnego. Pole służy do uzyskiwania dostępu do usługi w razie potrzeby w całej klasie.

W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane `WriteMessage` do wywoływania metody usługi:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Usługi wstrzyknięte do uruchamiania

Tylko następujące typy usług mogą `Startup` być wstrzykiwane do konstruktora podczas korzystania z hosta ogólnego (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Usługi można wstrzyknąć do: `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Usługi świadczone w ramach ram

Metoda `Startup.ConfigureServices` jest odpowiedzialna za definiowanie usług, które aplikacja używa, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC. Początkowo `IServiceCollection` dostarczone ma `ConfigureServices` usługi zdefiniowane przez strukturę w zależności od sposobu [hosta został skonfigurowany](xref:fundamentals/index#host). Nie jest rzadkością dla aplikacji na podstawie szablonu core ASP.NET mieć setki usług zarejestrowanych przez platformę. W poniższej tabeli wymieniono niewielką próbkę usług zarejestrowanych w ramach.

| Typ usługi | Okres istnienia |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Przejściowe |
| `IHostApplicationLifetime` | Singleton |
| `IWebHostEnvironment` | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Przejściowe |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Przejściowe |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Przejściowe |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Rejestrowanie dodatkowych usług przy pomocy metod rozszerzenia

Gdy metoda rozszerzenia kolekcji usług jest dostępna do zarejestrowania usługi (i jej usług `Add{SERVICE_NAME}` zależnych, jeśli jest to wymagane), konwencja jest użycie metody pojedynczego rozszerzenia, aby zarejestrować wszystkie usługi wymagane przez tę usługę. Poniższy kod jest przykładem dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzenia [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>

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

Aby uzyskać więcej <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> informacji, zobacz klasę w dokumentacji interfejsu API.

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi. ASP.NET usługi Core można skonfigurować z następującymi okresami istnienia:

### <a name="transient"></a>Przejściowe

Usługi przejściowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>lifetime ( ) są tworzone za każdym razem, gdy są wymagane z kontenera usługi. Ten okres istnienia działa najlepiej w przypadku lekkich, bezstanowych usług.

### <a name="scoped"></a>Zakresu

Usługi okresowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>o określonym zakresie ( ) są tworzone raz na żądanie klienta (połączenie).

> [!WARNING]
> Podczas korzystania z usługi o określonym zakresie w `Invoke` oprogramowaniu pośredniczącym, wstrzyknąć usługę do lub `InvokeAsync` metody. Nie wstrzyknąć za pomocą [iniekcji konstruktora,](xref:mvc/controllers/dependency-injection#constructor-injection) ponieważ wymusza usługę zachowywać się jak singleton. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Usługi okresu istnienia<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) są tworzone po `Startup.ConfigureServices` raz pierwszy są wymagane (lub po uruchomieniu i wystąpienie jest określony z rejestracją usługi). Każde kolejne żądanie używa tego samego wystąpienia. Jeśli aplikacja wymaga zachowania singleton, zaleca się zezwalanie kontenerowi usługi na zarządzanie okresem istnienia usługi. Nie implementuj wzorzec projektu singleton i podaj kod użytkownika do zarządzania okres istnienia obiektu w klasie.

> [!WARNING]
> Jest to niebezpieczne, aby rozwiązać usługi o określonym zakresie z singleton. Może to spowodować, że usługa ma niepoprawny stan podczas przetwarzania kolejnych żądań.

## <a name="service-registration-methods"></a>Metody rejestracji usługi

Metody rozszerzenia rejestracji usługi oferują przeciążenia, które są przydatne w określonych scenariuszach.

| Metoda | Automatyczny<br>obiekt<br>Dyspozycji | Wiele<br>implementacje | Przekaż args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<IMyDep, MyDep>();` | Tak | Tak | Nie |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Tak | Tak | Tak |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<MyDep>();` | Tak | Nie | Nie |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Nie | Tak | Tak |
| `AddSingleton(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Nie | Nie | Tak |

Aby uzyskać więcej informacji na temat usuwania typu, zobacz [usuwanie usług](#disposal-of-services) sekcji. Typowym scenariuszem dla wielu implementacji jest [szydercze typy do testowania.](xref:test/integration-tests#inject-mock-services)

`TryAdd{LIFETIME}`metody zarejestrować usługę tylko wtedy, gdy nie ma jeszcze zarejestrowanej implementacji.

W poniższym przykładzie pierwszy `MyDependency` wiersz `IMyDependency`rejestruje się dla . Drugi wiersz nie ma `IMyDependency` wpływu, ponieważ ma już zarejestrowaną implementację:

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

[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie ma jeszcze implementacji *tego samego typu.* Wiele usług jest `IEnumerable<{SERVICE}>`rozwiązywanych za pośrednictwem pliku . Podczas rejestrowania usług deweloper chce dodać wystąpienie tylko wtedy, gdy jeden z tego samego typu nie został jeszcze dodany. Ogólnie rzecz biorąc ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.

W poniższym przykładzie pierwszy `MyDep` wiersz `IMyDep1`rejestruje się dla . Druga linia rejestruje `MyDep` `IMyDep2`się dla . Trzecia linia nie ma `IMyDep1` wpływu, ponieważ `MyDep`ma już zarejestrowaną implementację:

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

Usługi można rozwiązać za pomocą dwóch mechanizmów:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności. `ActivatorUtilities`jest używany z abstrakcjami skierowanymi do użytkownika, takimi jak Pomocników tagów, kontrolery MVC i spinacze modelu.

Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.

Gdy usługi są `IServiceProvider` rozpoznawane przez lub `ActivatorUtilities` [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego.*

Gdy usługi są `ActivatorUtilities`rozpoznawane przez [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, że istnieje tylko jeden odpowiedni konstruktor. Przeciążenia konstruktora są obsługiwane, ale tylko jedno przeciążenie może istnieć, których argumenty mogą być spełnione przez iniekcję zależności.

## <a name="entity-framework-contexts"></a>Konteksty struktury encji

Konteksty entity framework są zwykle dodawane do kontenera usługi przy użyciu [okresu istnienia o określonym zakresie,](#service-lifetimes) ponieważ operacje bazy danych aplikacji sieci web są zwykle ograniczone do żądania klienta. Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) przeciążenie podczas rejestrowania kontekstu bazy danych. Usługi danego okresu istnienia nie należy używać kontekstu bazy danych o krótszym okresie istnienia niż usługa.

## <a name="lifetime-and-registration-options"></a>Opcje okresu istnienia i rejestracji

Aby zademonstrować różnicę między opcjami okresu istnienia i rejestracji, należy wziąć pod uwagę `OperationId`następujące interfejsy, które reprezentują zadania jako operację z unikatowym identyfikatorem . W zależności od tego, jak okres istnienia usługi operacyjnej jest skonfigurowany dla następujących interfejsów, kontener udostępnia takie samo lub inne wystąpienie usługi, gdy jest to wymagane przez klasę:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Interfejsy są implementowane `Operation` w klasie. Konstruktor `Operation` generuje identyfikator GUID, jeśli nie jest dostarczany:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

An `OperationService` jest zarejestrowany, który zależy `Operation` od każdego z innych typów. Gdy `OperationService` jest wymagane za pośrednictwem iniekcji zależności, odbiera nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.

* Gdy usługi przejściowe są tworzone na żądanie `OperationId` z `IOperationTransient` kontenera, `OperationId` usługa `OperationService`jest inna niż z . `OperationService`otrzymuje nowe wystąpienie `IOperationTransient` klasy. Nowe wystąpienie daje `OperationId`inny .
* Gdy usługi o określonym zakresie są `OperationId` tworzone `IOperationScoped` na żądanie klienta, `OperationService` usługa jest taka sama jak w żądaniu klienta. W przypadku żądań klientów `OperationId` obie usługi mają inną wartość.
* Gdy usługi pojedynczego i pojedynczego wystąpienia są tworzone raz i używane `OperationId` we wszystkich żądaniach klientów i wszystkich usługach, jest stała we wszystkich żądaniach usługi.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

W `Startup.ConfigureServices`, każdy typ jest dodawany do kontenera zgodnie z jego nazwany okres istnienia:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Usługa `IOperationSingletonInstance` używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty`. Jest jasne, gdy ten typ jest w użyciu (jego identyfikator GUID jest wszystkie zera).

Przykładowa aplikacja pokazuje okres istnienia obiektu w obrębie i między poszczególnymi żądaniami. Przykładowa aplikacja `IndexModel` żąda każdego `IOperation` rodzaju typu `OperationService`i . Następnie strona wyświetla wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Dwa następujące dane wyjściowe pokazuje wyniki dwóch żądań:

**Pierwsze żądanie:**

Operacje kontrolera:

Przemijające: d233e165-f417-469b-a866-1cf1935d2518  
Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

`OperationService`Operacji:

Przemijające: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

**Drugie żądanie:**

Operacje kontrolera:

Przemijające: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

`OperationService`Operacji:

Przejściowe: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

Należy przestrzegać, `OperationId` które z wartości różnią się w obrębie żądania i między żądaniami:

* *Obiekty przejściowe* są zawsze różne. Wartość przejściowa `OperationId` dla żądań pierwszego i drugiego `OperationService` klienta są różne dla operacji i żądań klientów. Nowe wystąpienie jest dostarczane do każdego żądania usługi i żądania klienta.
* *Obiekty o określonym zakresie* są takie same w żądaniu klienta, ale różne w żądaniach klientów.
* *Singleton* obiekty są takie same dla każdego obiektu `Operation` i każdego `Startup.ConfigureServices`żądania, niezależnie od tego, czy wystąpienie jest przewidziane w .

## <a name="call-services-from-main"></a>Zadzwoń do usług z głównych

Utwórz <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> za pomocą [IServiceScopeFactory.CreateScope,](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) aby rozwiązać usługi o określonym zakresie w zakresie aplikacji. Takie podejście jest przydatne do uzyskania dostępu do usługi o określonym zakresie podczas uruchamiania do uruchamiania zadań inicjowania. W poniższym przykładzie pokazano, `MyScopedService` jak `Program.Main`uzyskać kontekst dla w:

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

## <a name="scope-validation"></a>Sprawdzanie poprawności zakresu

Gdy aplikacja jest uruchomiona w środowisku deweloperskim i wywołuje [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) do utworzenia hosta, domyślny dostawca usług wykonuje kontrole, aby sprawdzić, czy:

* Usługi o określonym zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez głównego dostawcę usług.
* Usługi o określonym zakresie nie są bezpośrednio lub pośrednio wstrzykuje się do singletons.

Główny dostawca usług jest <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> tworzony, gdy jest wywoływana. Okres istnienia głównego dostawcy usług odpowiada okresowi istnienia aplikacji/serwera, gdy dostawca uruchamia się z aplikacją i jest usuwany po zamknięciu aplikacji.

Usługi o określonym zakresie są usuwane przez kontener, który je utworzył. Jeśli usługa o określonym zakresie jest tworzona w kontenerze głównym, okres istnienia usługi jest skutecznie promowany do singleton, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest zamknięty. Sprawdzanie poprawności zakresów usługi łapie te sytuacje, gdy `BuildServiceProvider` jest wywoływana.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Żądania usług

Usługi dostępne w ramach żądania core `HttpContext` ASP.NET są udostępniane za pośrednictwem [httpContext.RequestServices kolekcji.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)

Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji. Gdy obiekty określają zależności, są one spełnione `RequestServices`przez `ApplicationServices`typy znalezione w , nie .

Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio. Zamiast tego zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas i zezwalaj na platformę, aby wstrzyknąć zależności. Daje to klasy, które są łatwiejsze do przetestowania.

> [!NOTE]
> Preferuj żądanie zależności jako parametry konstruktora dostępu do `RequestServices` kolekcji.

## <a name="design-services-for-dependency-injection"></a>Usługi projektowe do iniekcji zależności

Najważniejsze wskazówki to:

* Projektowanie usług do korzystania z iniekcji zależności, aby uzyskać ich zależności.
* Unikaj klas stanowych, statycznych i członków. Projektowanie aplikacji do korzystania z usług singleton zamiast tego, które unikają tworzenia stanu globalnego.
* Unikaj bezpośredniego tworzenia wystąpienia klas zależnych w ramach usług. Bezpośrednie tworzenie wystąpienia łączy kod z określoną implementacją.
* Spraw, aby klasy aplikacji były małe, dobrze uwzględnione i łatwe do przetestowania.

Jeśli klasa wydaje się mieć zbyt wiele wstrzykniętych zależności, jest to zazwyczaj znak, że klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Spróbuj refaktoryzowania klasy, przenosząc niektóre z jego obowiązków do nowej klasy. Należy pamiętać, że klasy modelu strony Razor Pages i klasy kontrolera MVC powinny koncentrować się na problemach z interfejsem użytkownika. Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [odrębnych problemów.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)

### <a name="disposal-of-services"></a>Unieszkodliwianie usług

Kontener wymaga <xref:System.IDisposable.Dispose*> typów, <xref:System.IDisposable> które tworzy. Jeśli wystąpienie zostanie dodane do kontenera przez kod użytkownika, nie jest usuwany automatycznie.

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
* Okres istnienia zamierzonej usługi nie są znane przez strukturę.
* Struktura nie usuwa usług automatycznie.
* Jeśli usługi nie są jawnie usuwane w kodzie dewelopera, utrzymują się, dopóki aplikacja nie zostanie zamknięta.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

Aby zapoznać się z omówieniem opcji usuwania usług, zobacz [Cztery sposoby usuwania identyfikatorów w ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).

## <a name="default-service-container-replacement"></a>Domyślna wymiana kontenera usługi

Wbudowany kontener usługi został zaprojektowany do obsługi potrzeb struktury i większości aplikacji konsumenckich. Zaleca się użycie wbudowanego kontenera, chyba że potrzebna jest określona funkcja, której wbudowany kontener nie obsługuje, na przykład:

* Wstrzyknięcie właściwości
* Wstrzyknięcie na podstawie nazwy
* Pojemniki dla dzieci
* Niestandardowe zarządzanie okresem istnienia
* `Func<T>`obsługa inicjowania z opóźnieniem
* Rejestracja oparta na konwencji

Z ASP.NET aplikacjami Core można używać następujących kontenerów innych firm:

* [Autofac (autofac)](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Suchość](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Lekka Inject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Skrytka](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpieczeństwo wątkowe

Tworzenie usług singleton bezpiecznych dla wątków. Jeśli usługa singleton ma zależność od usługi przejściowej, usługi przejściowe mogą również wymagać bezpieczeństwa wątku w zależności od tego, jak jest używany przez singleton.

Metoda fabryczna pojedynczej usługi, takich jak drugi argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczne dla wątków. Podobnie jak`static`konstruktor typu ( ), jest gwarantowane, aby być wywoływane raz przez pojedynczy wątek.

## <a name="recommendations"></a>Zalecenia

* `async/await`i `Task` rozdzielczość usługi nie jest obsługiwana. C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec jest użycie metod asynchronicznych po synchronicznie rozwiązywania usługi.

* Należy unikać przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi. Na przykład koszyk użytkownika zazwyczaj nie powinny być dodawane do kontenera usługi. Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options). Podobnie należy unikać "posiadacza danych" obiektów, które istnieją tylko w celu umożliwienia dostępu do innego obiektu. Lepiej jest poprosić o rzeczywisty element za pośrednictwem DI.

* Unikaj statycznego dostępu do usług (na przykład statycznie wpisując [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użycia w innym miejscu).

* Należy unikać używania *wzorca lokalizatora usług*. Na przykład nie wywoływać, aby uzyskać wystąpienie <xref:System.IServiceProvider.GetService*> usługi, gdy można użyć DI zamiast:

  **Niepoprawne:**

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

  **Prawidłowe:**

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

* Inną odmianą lokalizatora usług, których należy unikać, jest wstrzykiwanie fabryki, która rozwiązuje zależności w czasie wykonywania. Obie te praktyki [mieszają inwersję](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategii kontroli.

* Unikaj dostępu statycznego (na `HttpContext` przykład [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Podobnie jak wszystkie zestawy zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane. Wyjątki są&mdash;rzadkie głównie szczególnych przypadkach w samych ramach.

DI jest *alternatywą dla* wzorców dostępu do obiektów statycznych/globalnych. Możesz nie być w stanie zrealizować korzyści z DI, jeśli mieszasz go z dostępem do obiektów statycznych.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Pisanie czystego kodu w ASP.NET Core z iniekcji zależności (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Zasada jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Odwrócenie kontenerów kontrolnych i wzorzec iniekcji zależności (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core obsługuje wzorzec projektowania oprogramowania wtrysku zależności (DI), który jest techniką osiągnięcia [inwersji kontroli (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

Aby uzyskać więcej informacji dotyczących iniekcji <xref:mvc/controllers/dependency-injection>zależności w kontrolerach MVC, zobacz .

[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Omówienie iniekcji zależności

*Zależność* jest dowolny obiekt, który wymaga innego obiektu. Sprawdź następującą `MyDependency` klasę `WriteMessage` za pomocą metody, od których zależą inne klasy w aplikacji:

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

Wystąpienie klasy `MyDependency` można utworzyć, aby `WriteMessage` udostępnić metodę do klasy. Klasa `MyDependency` jest zależnością `IndexModel` klasy:

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

Klasa tworzy i bezpośrednio zależy `MyDependency` od wystąpienia. Zależności kodu (takie jak poprzedni przykład) są problematyczne i należy ich unikać z następujących powodów:

* Aby `MyDependency` zastąpić inną implementację, klasa musi zostać zmodyfikowana.
* Jeśli `MyDependency` ma zależności, muszą być skonfigurowane przez klasę. W dużym projekcie z wieloma `MyDependency`klasami w zależności od kodu konfiguracji staje się rozproszone w całej aplikacji.
* Ta implementacja jest trudne do jednostkowego testu. Aplikacja powinna używać klasy `MyDependency` makiety lub skrótowej, co nie jest możliwe w przypadku tego podejścia.

Iniekcja zależności rozwiązuje następujące problemy poprzez:

* Użycie interfejsu lub klasy podstawowej do abstrakcyjnej implementacji zależności.
* Rejestracja zależności w kontenerze usługi. ASP.NET Core zapewnia wbudowany kontener serwisowy, <xref:System.IServiceProvider>. Usługi są rejestrowane w `Startup.ConfigureServices` metodzie aplikacji.
* *Wtrysk* usługi do konstruktora klasy, w której jest używana. Struktura bierze na siebie odpowiedzialność tworzenia wystąpienia zależności i usuwania go, gdy nie jest już potrzebne.

W [przykładowej](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)aplikacji `IMyDependency` interfejs definiuje metodę, którą usługa zapewnia aplikacji:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Ten interfejs jest realizowany przez `MyDependency`konkretny typ:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`żąda <xref:Microsoft.Extensions.Logging.ILogger`1> w jego konstruktorze. Nie jest niczym niezwykłym, aby użyć iniekcji zależności w sposób łańcuchowy. Każda żądana zależność z kolei żąda własnych zależności. Kontener rozpoznaje zależności na wykresie i zwraca w pełni rozwiązaną usługę. Zbiorczy zestaw zależności, które muszą zostać rozwiązane, jest zazwyczaj określany jako *drzewo zależności,* *wykres zależności*lub *wykres obiektu.*

`IMyDependency`i `ILogger<TCategoryName>` muszą być zarejestrowane w kontenerze serwisowym. `IMyDependency`jest zarejestrowana w `Startup.ConfigureServices`. `ILogger<TCategoryName>`jest zarejestrowany przez infrastrukturę abstrakcji rejestrowania, więc jest to [usługa świadczona przez framework](#framework-provided-services) zarejestrowana domyślnie przez platformę.

Kontener jest `ILogger<TCategoryName>` rozwiązywany, korzystając z [(ogólnych) otwartych typów,](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)eliminując konieczność rejestrowania każdego [(ogólnego) typu skonstruowanego:](/dotnet/csharp/language-reference/language-specification/types#constructed-types)

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

W przykładowej aplikacji `IMyDependency` usługa jest zarejestrowana `MyDependency`z typem betonu . Zakresy rejestracji okres istnienia usługi do okresu istnienia pojedynczego żądania. [Okresy istnienia usługi](#service-lifetimes) są opisane w dalszej części tego tematu.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Każda `services.Add{SERVICE_NAME}` metoda rozszerzenia dodaje (i potencjalnie konfiguruje) usługi. Na przykład `services.AddMvc()` dodaje usługi Razor Pages i MVC wymagają. Zaleca się, aby aplikacje postępowali zgodnie z tą konwencją. Umieść metody rozszerzenia w obszarze nazw [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) aby hermetyzować grupy rejestracji usług.

Jeśli konstruktor usługi wymaga [wbudowanego typu,](/dotnet/csharp/language-reference/keywords/built-in-types-table) `string`takiego jak , typ może zostać wstrzyknięty za pomocą [konfiguracji](xref:fundamentals/configuration/index) lub [wzorca opcji:](xref:fundamentals/configuration/options)

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

Wystąpienie usługi jest wymagane za pośrednictwem konstruktora klasy, w której usługa jest używana i przypisana do pola prywatnego. Pole służy do uzyskiwania dostępu do usługi w razie potrzeby w całej klasie.

W przykładowej aplikacji `IMyDependency` wystąpienie jest wymagane i używane `WriteMessage` do wywoływania metody usługi:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Usługi wstrzyknięte do uruchamiania

Tylko następujące typy usług mogą `Startup` być wstrzykiwane do konstruktora podczas korzystania z hosta ogólnego (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Usługi można wstrzyknąć do: `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Usługi świadczone w ramach ram

Metoda `Startup.ConfigureServices` jest odpowiedzialna za definiowanie usług, które aplikacja używa, w tym funkcje platformy, takie jak Entity Framework Core i ASP.NET Core MVC. Początkowo `IServiceCollection` dostarczone ma `ConfigureServices` usługi zdefiniowane przez strukturę w zależności od sposobu [hosta został skonfigurowany](xref:fundamentals/index#host). Nie jest rzadkością dla aplikacji na podstawie szablonu core ASP.NET mieć setki usług zarejestrowanych przez platformę. W poniższej tabeli wymieniono niewielką próbkę usług zarejestrowanych w ramach.

| Typ usługi | Okres istnienia |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Przejściowe |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Przejściowe |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Przejściowe |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Przejściowe |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Rejestrowanie dodatkowych usług przy pomocy metod rozszerzenia

Gdy metoda rozszerzenia kolekcji usług jest dostępna do zarejestrowania usługi (i jej usług `Add{SERVICE_NAME}` zależnych, jeśli jest to wymagane), konwencja jest użycie metody pojedynczego rozszerzenia, aby zarejestrować wszystkie usługi wymagane przez tę usługę. Poniższy kod jest przykładem dodawania dodatkowych usług do kontenera przy użyciu metod rozszerzenia [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) i: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>

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

Aby uzyskać więcej <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> informacji, zobacz klasę w dokumentacji interfejsu API.

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Wybierz odpowiedni okres istnienia dla każdej zarejestrowanej usługi. ASP.NET usługi Core można skonfigurować z następującymi okresami istnienia:

### <a name="transient"></a>Przejściowe

Usługi przejściowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>lifetime ( ) są tworzone za każdym razem, gdy są wymagane z kontenera usługi. Ten okres istnienia działa najlepiej w przypadku lekkich, bezstanowych usług.

### <a name="scoped"></a>Zakresu

Usługi okresowe<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>o określonym zakresie ( ) są tworzone raz na żądanie klienta (połączenie).

> [!WARNING]
> Podczas korzystania z usługi o określonym zakresie w `Invoke` oprogramowaniu pośredniczącym, wstrzyknąć usługę do lub `InvokeAsync` metody. Nie wstrzyknąć za pomocą [iniekcji konstruktora,](xref:mvc/controllers/dependency-injection#constructor-injection) ponieważ wymusza usługę zachowywać się jak singleton. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Usługi okresu istnienia<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) są tworzone po `Startup.ConfigureServices` raz pierwszy są wymagane (lub po uruchomieniu i wystąpienie jest określony z rejestracją usługi). Każde kolejne żądanie używa tego samego wystąpienia. Jeśli aplikacja wymaga zachowania singleton, zaleca się zezwalanie kontenerowi usługi na zarządzanie okresem istnienia usługi. Nie implementuj wzorzec projektu singleton i podaj kod użytkownika do zarządzania okres istnienia obiektu w klasie.

> [!WARNING]
> Jest to niebezpieczne, aby rozwiązać usługi o określonym zakresie z singleton. Może to spowodować, że usługa ma niepoprawny stan podczas przetwarzania kolejnych żądań.

## <a name="service-registration-methods"></a>Metody rejestracji usługi

Metody rozszerzenia rejestracji usługi oferują przeciążenia, które są przydatne w określonych scenariuszach.

| Metoda | Automatyczny<br>obiekt<br>Dyspozycji | Wiele<br>implementacje | Przekaż args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<IMyDep, MyDep>();` | Tak | Tak | Nie |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Tak | Tak | Tak |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Przykład:<br>`services.AddSingleton<MyDep>();` | Tak | Nie | Nie |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Nie | Tak | Tak |
| `AddSingleton(new {IMPLEMENTATION})`<br>Przykłady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Nie | Nie | Tak |

Aby uzyskać więcej informacji na temat usuwania typu, zobacz [usuwanie usług](#disposal-of-services) sekcji. Typowym scenariuszem dla wielu implementacji jest [szydercze typy do testowania.](xref:test/integration-tests#inject-mock-services)

`TryAdd{LIFETIME}`metody zarejestrować usługę tylko wtedy, gdy nie ma jeszcze zarejestrowanej implementacji.

W poniższym przykładzie pierwszy `MyDependency` wiersz `IMyDependency`rejestruje się dla . Drugi wiersz nie ma `IMyDependency` wpływu, ponieważ ma już zarejestrowaną implementację:

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

[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) rejestrują usługę tylko wtedy, gdy nie ma jeszcze implementacji *tego samego typu.* Wiele usług jest `IEnumerable<{SERVICE}>`rozwiązywanych za pośrednictwem pliku . Podczas rejestrowania usług deweloper chce dodać wystąpienie tylko wtedy, gdy jeden z tego samego typu nie został jeszcze dodany. Ogólnie rzecz biorąc ta metoda jest używana przez autorów biblioteki, aby uniknąć rejestrowania dwóch kopii wystąpienia w kontenerze.

W poniższym przykładzie pierwszy `MyDep` wiersz `IMyDep1`rejestruje się dla . Druga linia rejestruje `MyDep` `IMyDep2`się dla . Trzecia linia nie ma `IMyDep1` wpływu, ponieważ `MyDep`ma już zarejestrowaną implementację:

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

Usługi można rozwiązać za pomocą dwóch mechanizmów:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Zezwala na tworzenie obiektów bez rejestracji usługi w kontenerze iniekcji zależności. `ActivatorUtilities`jest używany z abstrakcjami skierowanymi do użytkownika, takimi jak Pomocników tagów, kontrolery MVC i spinacze modelu.

Konstruktory mogą akceptować argumenty, które nie są dostarczane przez iniekcję zależności, ale argumenty muszą przypisywać wartości domyślne.

Gdy usługi są `IServiceProvider` rozpoznawane przez lub `ActivatorUtilities` [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga konstruktora *publicznego.*

Gdy usługi są `ActivatorUtilities`rozpoznawane przez [, iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) wymaga, że istnieje tylko jeden odpowiedni konstruktor. Przeciążenia konstruktora są obsługiwane, ale tylko jedno przeciążenie może istnieć, których argumenty mogą być spełnione przez iniekcję zależności.

## <a name="entity-framework-contexts"></a>Konteksty struktury encji

Konteksty entity framework są zwykle dodawane do kontenera usługi przy użyciu [okresu istnienia o określonym zakresie,](#service-lifetimes) ponieważ operacje bazy danych aplikacji sieci web są zwykle ograniczone do żądania klienta. Domyślny okres istnienia jest objęty zakresem, jeśli okres istnienia nie jest określony przez [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) przeciążenie podczas rejestrowania kontekstu bazy danych. Usługi danego okresu istnienia nie należy używać kontekstu bazy danych o krótszym okresie istnienia niż usługa.

## <a name="lifetime-and-registration-options"></a>Opcje okresu istnienia i rejestracji

Aby zademonstrować różnicę między opcjami okresu istnienia i rejestracji, należy wziąć pod uwagę `OperationId`następujące interfejsy, które reprezentują zadania jako operację z unikatowym identyfikatorem . W zależności od tego, jak okres istnienia usługi operacyjnej jest skonfigurowany dla następujących interfejsów, kontener udostępnia takie samo lub inne wystąpienie usługi, gdy jest to wymagane przez klasę:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Interfejsy są implementowane `Operation` w klasie. Konstruktor `Operation` generuje identyfikator GUID, jeśli nie jest dostarczany:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

An `OperationService` jest zarejestrowany, który zależy `Operation` od każdego z innych typów. Gdy `OperationService` jest wymagane za pośrednictwem iniekcji zależności, odbiera nowe wystąpienie każdej usługi lub istniejące wystąpienie na podstawie okresu istnienia usługi zależnej.

* Gdy usługi przejściowe są tworzone na żądanie `OperationId` z `IOperationTransient` kontenera, `OperationId` usługa `OperationService`jest inna niż z . `OperationService`otrzymuje nowe wystąpienie `IOperationTransient` klasy. Nowe wystąpienie daje `OperationId`inny .
* Gdy usługi o określonym zakresie są `OperationId` tworzone `IOperationScoped` na żądanie klienta, `OperationService` usługa jest taka sama jak w żądaniu klienta. W przypadku żądań klientów `OperationId` obie usługi mają inną wartość.
* Gdy usługi pojedynczego i pojedynczego wystąpienia są tworzone raz i używane `OperationId` we wszystkich żądaniach klientów i wszystkich usługach, jest stała we wszystkich żądaniach usługi.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

W `Startup.ConfigureServices`, każdy typ jest dodawany do kontenera zgodnie z jego nazwany okres istnienia:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Usługa `IOperationSingletonInstance` używa określonego wystąpienia o znanym identyfikatorze `Guid.Empty`. Jest jasne, gdy ten typ jest w użyciu (jego identyfikator GUID jest wszystkie zera).

Przykładowa aplikacja pokazuje okres istnienia obiektu w obrębie i między poszczególnymi żądaniami. Przykładowa aplikacja `IndexModel` żąda każdego `IOperation` rodzaju typu `OperationService`i . Następnie strona wyświetla wszystkie `OperationId` wartości klasy modelu strony i usługi za pomocą przypisań właściwości:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Dwa następujące dane wyjściowe pokazuje wyniki dwóch żądań:

**Pierwsze żądanie:**

Operacje kontrolera:

Przemijające: d233e165-f417-469b-a866-1cf1935d2518  
Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

`OperationService`Operacji:

Przemijające: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Zakres: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

**Drugie żądanie:**

Operacje kontrolera:

Przemijające: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

`OperationService`Operacji:

Przejściowe: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Zakres: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Wystąpienie: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000 00000000000000

Należy przestrzegać, `OperationId` które z wartości różnią się w obrębie żądania i między żądaniami:

* *Obiekty przejściowe* są zawsze różne. Wartość przejściowa `OperationId` dla żądań pierwszego i drugiego `OperationService` klienta są różne dla operacji i żądań klientów. Nowe wystąpienie jest dostarczane do każdego żądania usługi i żądania klienta.
* *Obiekty o określonym zakresie* są takie same w żądaniu klienta, ale różne w żądaniach klientów.
* *Singleton* obiekty są takie same dla każdego obiektu `Operation` i każdego `Startup.ConfigureServices`żądania, niezależnie od tego, czy wystąpienie jest przewidziane w .

## <a name="call-services-from-main"></a>Zadzwoń do usług z głównych

Utwórz <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> za pomocą [IServiceScopeFactory.CreateScope,](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) aby rozwiązać usługi o określonym zakresie w zakresie aplikacji. Takie podejście jest przydatne do uzyskania dostępu do usługi o określonym zakresie podczas uruchamiania do uruchamiania zadań inicjowania. W poniższym przykładzie pokazano, `MyScopedService` jak `Program.Main`uzyskać kontekst dla w:

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

## <a name="scope-validation"></a>Sprawdzanie poprawności zakresu

Gdy aplikacja jest uruchomiona w środowisku programistycznym, domyślny dostawca usług wykonuje kontrole, aby sprawdzić, czy:

* Usługi o określonym zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez głównego dostawcę usług.
* Usługi o określonym zakresie nie są bezpośrednio lub pośrednio wstrzykuje się do singletons.

Główny dostawca usług jest <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> tworzony, gdy jest wywoływana. Okres istnienia głównego dostawcy usług odpowiada okresowi istnienia aplikacji/serwera, gdy dostawca uruchamia się z aplikacją i jest usuwany po zamknięciu aplikacji.

Usługi o określonym zakresie są usuwane przez kontener, który je utworzył. Jeśli usługa o określonym zakresie jest tworzona w kontenerze głównym, okres istnienia usługi jest skutecznie promowany do singleton, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest zamknięty. Sprawdzanie poprawności zakresów usługi łapie te sytuacje, gdy `BuildServiceProvider` jest wywoływana.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Żądania usług

Usługi dostępne w ramach żądania core `HttpContext` ASP.NET są udostępniane za pośrednictwem [httpContext.RequestServices kolekcji.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)

Usługi żądania reprezentują usługi skonfigurowane i żądane jako część aplikacji. Gdy obiekty określają zależności, są one spełnione `RequestServices`przez `ApplicationServices`typy znalezione w , nie .

Ogólnie rzecz biorąc aplikacja nie powinna używać tych właściwości bezpośrednio. Zamiast tego zażądaj typów, które klasy wymagają za pośrednictwem konstruktorów klas i zezwalaj na platformę wstrzyknąć zależności. Daje to klasy, które są łatwiejsze do przetestowania.

> [!NOTE]
> Preferuj żądanie zależności jako parametry konstruktora dostępu do `RequestServices` kolekcji.

## <a name="design-services-for-dependency-injection"></a>Usługi projektowe do iniekcji zależności

Najważniejsze wskazówki to:

* Projektowanie usług do korzystania z iniekcji zależności, aby uzyskać ich zależności.
* Unikaj klas stanowych, statycznych i członków. Projektowanie aplikacji do korzystania z usług singleton zamiast tego, które unikają tworzenia stanu globalnego.
* Unikaj bezpośredniego tworzenia wystąpienia klas zależnych w ramach usług. Bezpośrednie tworzenie wystąpienia łączy kod z określoną implementacją.
* Spraw, aby klasy aplikacji były małe, dobrze uwzględnione i łatwe do przetestowania.

Jeśli klasa wydaje się mieć zbyt wiele wstrzykniętych zależności, jest to zazwyczaj znak, że klasa ma zbyt wiele obowiązków i narusza [zasadę pojedynczej odpowiedzialności (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Spróbuj refaktoryzowania klasy, przenosząc niektóre z jego obowiązków do nowej klasy. Należy pamiętać, że klasy modelu strony Razor Pages i klasy kontrolera MVC powinny koncentrować się na problemach z interfejsem użytkownika. Reguły biznesowe i szczegóły implementacji dostępu do danych powinny być przechowywane w klasach odpowiednich do tych [odrębnych problemów.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)

### <a name="disposal-of-services"></a>Unieszkodliwianie usług

Kontener wymaga <xref:System.IDisposable.Dispose*> typów, <xref:System.IDisposable> które tworzy. Jeśli wystąpienie zostanie dodane do kontenera przez kod użytkownika, nie jest usuwany automatycznie.

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
* Okres istnienia zamierzonej usługi nie są znane przez strukturę.
* Struktura nie usuwa usług automatycznie.
* Jeśli usługi nie są jawnie usuwane w kodzie dewelopera, utrzymują się, dopóki aplikacja nie zostanie zamknięta.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a>Domyślna wymiana kontenera usługi

Wbudowany kontener usługi został zaprojektowany do obsługi potrzeb struktury i większości aplikacji konsumenckich. Zaleca się użycie wbudowanego kontenera, chyba że potrzebna jest określona funkcja, której wbudowany kontener nie obsługuje, na przykład:

* Wstrzyknięcie właściwości
* Wstrzyknięcie na podstawie nazwy
* Pojemniki dla dzieci
* Niestandardowe zarządzanie okresem istnienia
* `Func<T>`obsługa inicjowania z opóźnieniem
* Rejestracja oparta na konwencji

Z ASP.NET aplikacjami Core można używać następujących kontenerów innych firm:

* [Autofac (autofac)](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [Suchość](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Lekka Inject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Skrytka](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpieczeństwo wątkowe

Tworzenie usług singleton bezpiecznych dla wątków. Jeśli usługa singleton ma zależność od usługi przejściowej, usługi przejściowe mogą również wymagać bezpieczeństwa wątku w zależności od tego, jak jest używany przez singleton.

Metoda fabryczna pojedynczej usługi, takich jak drugi argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nie musi być bezpieczne dla wątków. Podobnie jak`static`konstruktor typu ( ), jest gwarantowane, aby być wywoływane raz przez pojedynczy wątek.

## <a name="recommendations"></a>Zalecenia

* `async/await`i `Task` rozdzielczość usługi nie jest obsługiwana. C# nie obsługuje konstruktorów asynchronicznych; w związku z tym zalecany wzorzec jest użycie metod asynchronicznych po synchronicznie rozwiązywania usługi.

* Należy unikać przechowywania danych i konfiguracji bezpośrednio w kontenerze usługi. Na przykład koszyk użytkownika zazwyczaj nie powinny być dodawane do kontenera usługi. Konfiguracja powinna używać [wzorca opcji](xref:fundamentals/configuration/options). Podobnie należy unikać "posiadacza danych" obiektów, które istnieją tylko w celu umożliwienia dostępu do innego obiektu. Lepiej jest poprosić o rzeczywisty element za pośrednictwem DI.

* Unikaj statycznego dostępu do usług (na przykład statycznie wpisując [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) do użycia w innym miejscu).

* Należy unikać używania *wzorca lokalizatora usług,* który łączy [inwersję](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategii kontroli.

  * Nie wywoływać, <xref:System.IServiceProvider.GetService*> aby uzyskać wystąpienie usługi, gdy można użyć DI zamiast:

    **Niepoprawne:**

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

    **Prawidłowe:**

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

  * Należy unikać wstrzykiwania fabryki, która rozwiązuje <xref:System.IServiceProvider.GetService*>zależności w czasie wykonywania przy użyciu .

* Unikaj dostępu statycznego (na `HttpContext` przykład [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Podobnie jak wszystkie zestawy zaleceń, mogą wystąpić sytuacje, w których ignorowanie zalecenia jest wymagane. Wyjątki są&mdash;rzadkie głównie szczególnych przypadkach w samych ramach.

DI jest *alternatywą dla* wzorców dostępu do obiektów statycznych/globalnych. Możesz nie być w stanie zrealizować korzyści z DI, jeśli mieszasz go z dostępem do obiektów statycznych.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Pisanie czystego kodu w ASP.NET Core z iniekcji zależności (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Zasada jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Odwrócenie kontenerów kontrolnych i wzorzec iniekcji zależności (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Jak zarejestrować usługę z wieloma interfejsami w ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
