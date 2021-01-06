---
title: ASP.NET Core Blazor wstrzykiwania zależności
author: guardrex
description: Dowiedz się, jak Blazor aplikacje mogą wstrzyknąć usługi do składników programu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3f2b4eff5422acbec80b2fd9b801101271cc3f75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97808728"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>ASP.NET Core Blazor wstrzykiwania zależności

Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)

[Iniekcja zależności (di)](xref:fundamentals/dependency-injection) to technika uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji:

* Usługi zarejestrowane w ramach platformy można wstrzyknąć bezpośrednio do składników Blazor aplikacji.
* Blazor aplikacje definiują i rejestrują niestandardowe usługi i udostępniają je w całej aplikacji za pośrednictwem programu DI.

## <a name="default-services"></a>Usługi domyślne

Usługi przedstawione w poniższej tabeli są zwykle używane w Blazor aplikacjach.

| Usługa | Okres istnienia | Opis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Zakresie | <p>Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.</p><p>Wystąpienie <xref:System.Net.Http.HttpClient> w Blazor WebAssembly aplikacji używa przeglądarki do obsługi ruchu HTTP w tle.</p><p>Blazor Server aplikacje nie domyślnie zawierają <xref:System.Net.Http.HttpClient> skonfigurowane jako usługa. Udostępnianie <xref:System.Net.Http.HttpClient> Blazor Server aplikacji.</p><p>Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>.</p><p><xref:System.Net.Http.HttpClient>Jest zarejestrowany jako usługa o określonym zakresie, a nie pojedyncza. Aby uzyskać więcej informacji, zobacz sekcję [okres istnienia usługi](#service-lifetime) .</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: W zakresie</p> | Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript. Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly**: Singleton</p><p>**Blazor Server**: W zakresie</p> | Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji. Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli. W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.

## <a name="add-services-to-an-app"></a>Dodawanie usług do aplikacji

::: zone pivot="webassembly"

Skonfiguruj usługi dla kolekcji usług aplikacji w `Program.Main` metodzie `Program.cs` . W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency` :

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

Po skompilowaniu hosta usługi są dostępne z poziomu korzenia DI przed renderowaniem wszystkich składników. Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

Host udostępnia centralne wystąpienie konfiguracji dla aplikacji. W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład `appsettings.json` ) do `InitializeWeatherAsync` :

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

Po utworzeniu nowej aplikacji zapoznaj się z tą `Startup.ConfigureServices` metodą w `Startup.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda jest przenoszona <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a, która jest listą obiektów [deskryptora usługi](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) . Usługi są dodawane w `ConfigureServices` metodzie przez dostarczenie deskryptorów usługi do kolekcji usług. Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>Okres istnienia usługi

Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.

| Okres istnienia | Opis |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Blazor WebAssembly aplikacje nie mają obecnie koncepcji DI Scopes. `Scoped`-zarejestrowane usługi zachowują się jak `Singleton` usługi.</p><p>Blazor ServerModel hostingu obsługuje `Scoped` okres istnienia w żądaniach HTTP, ale nie między SignalR komunikatami połączenia/obwodu między składnikami, które są ładowane na komputerze klienckim. RazorCzęści strony lub MVC w aplikacji traktują usługi o określonym zakresie w normalny sposób i ponownie tworzy usługi na *każdym żądaniu HTTP* podczas nawigowania między stronami lub widokami albo ze strony lub widoku do składnika. Usługi o określonym zakresie nie są odtworzone podczas nawigowania między składnikami na kliencie, gdzie komunikacja z serwerem odbywa się przez SignalR połączenie obwodu użytkownika, a nie za pośrednictwem żądań HTTP. W następujących scenariuszach składników na kliencie usługi o określonym zakresie są odtworzone, ponieważ dla użytkownika zostanie utworzony nowy obwód:</p><ul><li>Użytkownik zamknie okno przeglądarki. Użytkownik otworzy nowe okno i nawiguje z powrotem do aplikacji.</li><li>Użytkownik zamknie ostatnią kartę aplikacji w oknie przeglądarki. Użytkownik otworzy nową kartę i nawiguje z powrotem do aplikacji.</li><li>Użytkownik wybiera przycisk Załaduj ponownie/Odśwież w przeglądarce.</li></ul><p>Aby uzyskać więcej informacji o zachowaniu stanu użytkownika w ramach usług w zakresie w Blazor Server aplikacjach, zobacz <xref:blazor/hosting-models?pivots=server> .</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI tworzy *pojedyncze wystąpienie* usługi. Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi. |

System DI jest oparty na systemie DI w ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Żądanie usługi w składniku

Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywy, która ma dwa parametry:

* Typ: typ usługi do dodania.
* Property: Nazwa właściwości otrzymującej wstrzykiwaną usługę App Service. Właściwość nie wymaga ręcznego tworzenia. Kompilator tworzy właściwość.

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.

Użyj wielu [`@inject`](xref:mvc/views/razor#inject) instrukcji, aby wstrzyknąć różne usługi.

Poniższy przykład pokazuje, jak używać [`@inject`](xref:mvc/views/razor#inject) . Implementowanie usługi `Services.IDataAccess` jest wstrzykiwane do właściwości składnika `DataRepository` . Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

Wewnętrznie wygenerowana Właściwość ( `DataRepository` ) używa [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu. Zazwyczaj ten atrybut nie jest używany bezpośrednio. Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut:

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

W składnikach pochodnych klasy bazowej [`@inject`](xref:mvc/views/razor#inject) dyrektywa nie jest wymagana. <xref:Microsoft.AspNetCore.Components.InjectAttribute>Klasa bazowa jest wystarczająca:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Korzystanie z usług DI w

Złożone usługi mogą wymagać dodatkowych usług. W poniższym przykładzie `DataAccess` wymaga <xref:System.Net.Http.HttpClient> usługi domyślnej. [`@inject`](xref:mvc/views/razor#inject) (lub [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut) nie jest dostępny do użytku w usługach. Zamiast tego należy użyć *iniekcji konstruktora* . Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi. Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio. W poniższym przykładzie Konstruktor odbiera <xref:System.Net.Http.HttpClient> przez di. <xref:System.Net.Http.HttpClient> jest domyślną usługą.

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

Wymagania wstępne dotyczące iniekcji konstruktora:

* Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI. Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.
* Odpowiedni Konstruktor musi być `public` .
* Musi istnieć jeden odpowiedni Konstruktor. W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Klasy składników podstawowych narzędzi do zarządzania DI zakresem

W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania. Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI. W Blazor Server aplikacjach zakres żądań jest stosowany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano. W Blazor WebAssembly aplikacjach usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.

> [!NOTE]
> Aby wykryć jednorazowe usługi przejściowe w aplikacji, zobacz sekcję [wykrywanie przejściowych](#detect-transient-disposables) elementów bezwzględnych.

Zastosowanie tego typu w aplikacjach ogranicza okres istnienia usługi Blazor <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . <xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym typem pochodnym <xref:Microsoft.AspNetCore.Components.ComponentBase> , który tworzy zakres di odpowiadający okresowi istnienia składnika. Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika. Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte. Może to być przydatne w przypadku usług, które:

* Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.
* Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.

Dostępne są dwie wersje <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typu:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> jest abstrakcyjnym, jednorazowym elementem podrzędnym <xref:Microsoft.AspNetCore.Components.ComponentBase> typu z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> właściwością chronioną typu <xref:System.IServiceProvider> . Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.

  Program DI Services wprowadzany do składnika przy użyciu [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu lub nie jest tworzony w zakresie składnika. Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> lub <xref:System.IServiceProvider.GetService%2A> . Wszystkie usługi rozpoznane przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> dostawcy mają swoje zależności z tego samego zakresu.

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> pochodzi z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i dodaje <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> Właściwość zwracającą wystąpienie `T` z dostawcy i zakresu. Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia, <xref:System.IServiceProvider> gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika. Ta <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> Właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>Użycie Entity Framework Core (EF Core) DbContext z elementu DI

Aby uzyskać więcej informacji, zobacz <xref:blazor/blazor-server-ef-core>.

## <a name="detect-transient-disposables"></a>Wykrywanie przejściowych jednorazowych

Poniższe przykłady przedstawiają sposób wykrywania jednorazowych usług przejściowych w aplikacji, która powinna być używana <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Aby uzyskać więcej informacji, zobacz [klasy składników podstawowych narzędzi, aby zarządzać sekcją di Scope](#utility-base-component-classes-to-manage-a-di-scope) .

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

`TransientDisposable`Wykryto w poniższym przykładzie ( `Program.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> do `Program.cs` :

```csharp
using Microsoft.Extensions.DependencyInjection;
```

W `Program.CreateHostBuilder` programie `Program.cs` :

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

`TransientDependency`Wykryto w poniższym przykładzie ( `Startup.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

Aplikacja może rejestrować przejściowe jednorazowe, bez zgłaszania wyjątku. Jednak próba rozpoznania przejściowych wyników jednorazowych w <xref:System.InvalidOperationException> , jak pokazano w poniższym przykładzie.

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

Przejdź do `TransientDisposable` składnika w `/transient-disposable` i <xref:System.InvalidOperationException> jest generowany, gdy struktura próbuje utworzyć wystąpienie `TransientDisposable` :

> System. InvalidOperationException: próba rozpoznania przejściowej jednorazowej usługi TransientDisposable w niewłaściwym zakresie. Użyj \<T> klasy bazowej składnika "OwningComponentBase" dla usługi "t", którą próbujesz rozwiązać.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/dependency-injection>
* [`IDisposable` Wskazówki dotyczące wystąpień przejściowych i współużytkowanych](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
