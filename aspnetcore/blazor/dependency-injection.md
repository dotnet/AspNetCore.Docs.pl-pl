---
title: ASP.NET Iniekcja zależności rdzenia Blazor
author: guardrex
description: Zobacz, Blazor jak aplikacje mogą wstrzyknąć usługi do składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658075"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET rdzeń blazor uzależnienia iniekcji

Przez [Rainer Stropek](https://www.timecockpit.com) i [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor obsługuje [wstrzykiwanie zależności (DI)](xref:fundamentals/dependency-injection). Aplikacje mogą korzystać z wbudowanych usług, wstrzykując je do składników. Aplikacje mogą również definiować i rejestrować usługi niestandardowe i udostępniać je w całej aplikacji za pośrednictwem DI.

DI to technika uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji. Może to być przydatne w aplikacjach Blazor do:

* Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanych jako usługa *singleton.*
* Odłącz komponenty od klas usług betonowych przy użyciu abstrakcji odwołań. Rozważmy na przykład `IDataAccess` interfejs dostępu do danych w aplikacji. Interfejs jest implementowany przez `DataAccess` klasę betonu i zarejestrowany jako usługa w kontenerze usługi aplikacji. Gdy składnik używa DI do `IDataAccess` odbierania implementacji, składnik nie jest sprzężona z typem betonu. Implementacja może być zamieniona, być może dla implementacji makiety w testach jednostkowych.

## <a name="default-services"></a>Usługi domyślne

Usługi domyślne są automatycznie dodawane do kolekcji usług aplikacji.

| Usługa | Okres istnienia | Opis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Zawiera metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.<br><br>Wystąpienie `HttpClient` w aplikacji Blazor WebAssembly używa przeglądarki do obsługi ruchu HTTP w tle.<br><br>Aplikacje Blazor Server domyślnie nie zawierają skonfigurowanej `HttpClient` jako usługi. Podaj `HttpClient` aplikację Blazor Server.<br><br>Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Zakres (Serwer Blazor) | Reprezentuje wystąpienie środowiska wykonawczego JavaScript, w którym są wywoływane wywołania JavaScript. Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Zakres (Serwer Blazor) | Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji. Aby uzyskać więcej informacji, zobacz [URI i pomocników stanu nawigacji](xref:blazor/routing#uri-and-navigation-state-helpers). |

Dostawca usług niestandardowych nie udostępnia automatycznie usług domyślnych wymienionych w tabeli. Jeśli korzystasz z niestandardowego dostawcy usług i wymagasz dowolnej z usług wyświetlanych w tabeli, dodaj wymagane usługi do nowego dostawcy usług.

## <a name="add-services-to-an-app"></a>Dodawanie usług do aplikacji

### <a name="blazor-webassembly"></a>Zestaw WebAssembly Blazor

Skonfiguruj usługi dla kolekcji `Main` usług aplikacji w metodzie *Program.cs*. W poniższym przykładzie implementacja jest zarejestrowana `MyDependency` dla: `IMyDependency`

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

Po zbudowaniu hosta usługi są dostępne z głównego zakresu DI przed renderowaniem jakichkolwiek składników. Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji. Opierając się na poprzednim przykładzie, adres URL usługi pogodowej jest przekazywany z domyślnego źródła `InitializeWeatherAsync`konfiguracji (na przykład *appsettings.json)* do:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Serwer Blazor

Po utworzeniu nowej aplikacji `Startup.ConfigureServices` sprawdź metodę:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Metoda `ConfigureServices` jest przekazywana , <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>który jest listą<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>obiektów deskryptora usługi ( ). Usługi są dodawane przez zapewnienie deskryptorów usługi do kolekcji usługi. Poniższy przykład przedstawia koncepcję `IDataAccess` z interfejsem `DataAccess`i jego konkretną implementacją:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Okres istnienia usługi

Usługi można skonfigurować z okresami istnienia pokazanymi w poniższej tabeli.

| Okres istnienia | Opis |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorAplikacje WebAssembly nie mają obecnie pojęcia zakresów DI. `Scoped`-zarejestrowanych usług `Singleton` zachowują się jak usługi. Jednak model Blazor hostingu serwera `Scoped` obsługuje okres istnienia. W Blazor aplikacjach serwera rejestracja usługi o określonym zakresie jest o zakresie *do połączenia*. Z tego powodu przy użyciu usług o określonym zakresie jest preferowane dla usług, które powinny być ograniczone do bieżącego użytkownika, nawet jeśli bieżącą intencją jest uruchomienie po stronie klienta w przeglądarce. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI tworzy *pojedyncze wystąpienie* usługi. Wszystkie składniki wymagające `Singleton` usługi otrzymują wystąpienie tej samej usługi. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Za każdym razem, gdy składnik `Transient` uzyskuje wystąpienie usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi. |

System DI jest oparty na systemie DI w ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Żądanie usługi w składniku

Po dodaniu usług do kolekcji usługi wstrzyknąć usługi do składników przy użyciu [ \@dyrektywy Razor wstrzyknąć.](xref:mvc/views/razor#inject) `@inject`posiada dwa parametry:

* Wpisz &ndash; typ usługi do wstrzyknięcia.
* Właściwość &ndash; Nazwa właściwości odbierającej wstrzykniętą usługę aplikacji. Właściwość nie wymaga ręcznego tworzenia. Kompilator tworzy właściwość.

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.

Użyj `@inject` wielu instrukcji, aby wstrzyknąć różne usługi.

W poniższym przykładzie `@inject`pokazano, jak używać . Implementacja `Services.IDataAccess` usługi jest wstrzykuje `DataRepository`się do właściwości składnika . Należy zauważyć, jak kod `IDataAccess` jest tylko przy użyciu abstrakcji:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Wewnętrznie wygenerowana właściwość`DataRepository`( ) `InjectAttribute` używa atrybutu. Zazwyczaj ten atrybut nie jest używany bezpośrednio. Jeśli klasa podstawowa jest wymagana dla komponentów i właściwości wtryskiwanych są `InjectAttribute`również wymagane dla klasy podstawowej, należy ręcznie dodać:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

W składnikach pochodzących z `@inject` klasy podstawowej dyrektywa nie jest wymagana. Klasa `InjectAttribute` podstawowa jest wystarczająca:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Używanie DI w usługach

Złożone usługi mogą wymagać dodatkowych usług. W poprzednim przykładzie `DataAccess` może `HttpClient` wymagać usługi domyślnej. `@inject`(lub `InjectAttribute`) nie jest dostępny do użytku w usługach. Zamiast tego należy użyć *wtrysku konstruktora.* Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi. Gdy DI tworzy usługę, rozpoznaje usługi, które wymaga w konstruktorze i zapewnia je odpowiednio.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Wymagania wstępne dotyczące iniekcji konstruktora:

* Jeden konstruktor musi istnieć, którego argumenty mogą być spełnione przez DI. Dodatkowe parametry nieobjęte DI są dozwolone, jeśli określają wartości domyślne.
* Odpowiedni konstruktor musi być *publiczny.*
* Jeden odpowiedni konstruktor musi istnieć. W przypadku niejednoznaczności DI zgłasza wyjątek.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Klasy składników podstawowych narzędzia do zarządzania zakresem DI

W aplikacjach ASP.NET Core usługi o określonym zakresie są zazwyczaj ograniczone do bieżącego żądania. Po zakończeniu żądania wszelkie usługi o określonym zakresie lub przejściowe są usuwane przez system DI. W Blazor aplikacjach serwera zakres żądania trwa przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i o określonym zakresie będą żyły znacznie dłużej niż oczekiwano. W Blazor aplikacjach WebAssembly usługi zarejestrowane z okresem istnienia o określonym zakresie są traktowane jako singletony, więc żyją dłużej niż usługi o określonym zakresie w typowych aplikacjach ASP.NET Core.

Podejście, które ogranicza okres Blazor istnienia usługi `OwningComponentBase` w aplikacjach jest użycie typu. `OwningComponentBase`jest typem abstrakcyjnym `ComponentBase` pochodzącym z tego tworzy zakres DI odpowiadający okres istnienia składnika. Przy użyciu tego zakresu, jest możliwe do korzystania z usług DI o okresie istnienia o określonym zakresie i mieć je na żywo tak długo, jak składnik. Gdy składnik zostanie zniszczony, usługi od dostawcy usług o określonym zakresie składnika są również usuwane. Może to być przydatne w przypadku usług, które:

* Powinny być ponownie w obrębie składnika, jak okres przejściowy jest nieodpowiednie.
* Nie powinny być współużytkowane przez składniki, jak okres istnienia singleton jest niewłaściwe.

Dostępne są `OwningComponentBase` dwie wersje typu:

* `OwningComponentBase`jest abstrakcyjnym, jednorazowym `ComponentBase` dzieckiem typu `ScopedServices` z chroniona właściwość typu `IServiceProvider`. Ten dostawca może służyć do rozpoznawania usług, które są ograniczone do okresu istnienia składnika.

  Usługi DI wstrzyknięte `InjectAttribute` do`[Inject]`składnika przy użyciu `@inject` lub ( ) nie są tworzone w zakresie składnika. Aby korzystać z zakresu składnika, usługi `ScopedServices.GetRequiredService` `ScopedServices.GetService`muszą być rozwiązane za pomocą lub . Wszystkie usługi rozwiązane `ScopedServices` przy użyciu dostawcy mają swoje zależności dostarczone z tego samego zakresu.

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* `OwningComponentBase<T>`pochodzi z `OwningComponentBase` i dodaje `Service` właściwość, `T` która zwraca wystąpienie z dostawcy DI o określonym zakresie. Ten typ jest wygodnym sposobem dostępu do usług `IServiceProvider` o określonym zakresie bez użycia wystąpienia, gdy istnieje jedna usługa podstawowa, którą aplikacja wymaga z kontenera DI przy użyciu zakresu składnika. Właściwość `ScopedServices` jest dostępna, dzięki czemu aplikacja może uzyskać usługi innych typów, jeśli to konieczne.

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Korzystanie z entity framework DbContext z DI

Jednym z typowych typów usług do pobrania z DI `DbContext` w aplikacjach sieci web jest Entity Framework (EF) obiektów. Rejestrowanie usług `IServiceCollection.AddDbContext` EF `DbContext` przy użyciu dodaje jako usługi o określonym zakresie domyślnie. Rejestrowanie jako usługa o określonym zakresie Blazor może prowadzić `DbContext` do problemów w aplikacjach, ponieważ powoduje, że wystąpienia mają być długotrwałe i udostępniane w aplikacji. `DbContext`nie jest bezpieczny dla wątków i nie może być używany jednocześnie.

W zależności od `OwningComponentBase` aplikacji, za pomocą `DbContext` ograniczenia zakresu do jednego składnika *może* rozwiązać problem. Jeśli `DbContext` składnik nie używa równolegle, wyprowadzanie składnika `OwningComponentBase` z i `DbContext` `ScopedServices` pobieranie z jest wystarczające, ponieważ zapewnia, że:

* Oddzielne składniki nie współużytkuje `DbContext`.
* Żyje `DbContext` tylko tak długo, jak składnik w zależności od niego.

Jeśli pojedynczy składnik może `DbContext` używać jednocześnie (na przykład za każdym razem, gdy `OwningComponentBase` użytkownik wybierze przycisk), nawet przy użyciu nie unika problemów z równoczesnych operacji EF. W takim przypadku należy `DbContext` użyć innego dla każdej operacji logicznego EF. Użyj jednej z następujących metod:

* Utwórz `DbContext` bezpośrednio `DbContextOptions<TContext>` jako argument, który może być pobierany z DI i jest bezpieczny dla wątków.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* Zarejestruj `DbContext` w kontenerze usługi z przejściowym okresem istnienia:
  * Podczas rejestrowania kontekstu `ServiceLifetime.Transient`należy użyć pliku . Metoda `AddDbContext` rozszerzenia przyjmuje dwa opcjonalne parametry typu `ServiceLifetime`. Aby użyć tego podejścia, tylko `contextLifetime` `ServiceLifetime.Transient`parametr musi być . `optionsLifetime`może zachować domyślną wartość . `ServiceLifetime.Scoped`

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Przejściowy `DbContext` może być wstrzykiwany normalnie (przy użyciu) `@inject`do składników, które nie będą wykonywać wiele operacji EF równolegle. Te, które mogą wykonywać wiele operacji `DbContext` EF jednocześnie `IServiceProvider.GetRequiredService`można zażądać oddzielnych obiektów dla każdej operacji równoległej przy użyciu .

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
