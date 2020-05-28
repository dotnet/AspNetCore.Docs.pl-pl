---
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor wstrzykiwania zależności

Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Jan Rousos](https://github.com/mjrousos)

Blazorobsługuje [iniekcję zależności (di)](xref:fundamentals/dependency-injection). Aplikacje mogą używać wbudowanych usług, wprowadzając je do składników programu. Aplikacje mogą także definiować i rejestrować niestandardowe usługi i udostępniać je w całej aplikacji za pomocą funkcji DI.

DI jest techniką uzyskiwania dostępu do usług skonfigurowanych w centralnej lokalizacji. Może to być przydatne w Blazor aplikacjach, aby:

* Udostępnianie pojedynczego wystąpienia klasy usługi w wielu składnikach, znanej jako *pojedyncze usługi.*
* Oddziel składniki od klas konkretnych usług za pomocą abstrakcji odwołań. Rozważmy na przykład interfejs `IDataAccess` do uzyskiwania dostępu do danych w aplikacji. Interfejs jest implementowany przez konkretną `DataAccess` klasę i zarejestrowany jako usługa w kontenerze usługi aplikacji. Gdy składnik używa elementu DI do odbierania `IDataAccess` implementacji, składnik nie jest połączony z konkretnym typem. Implementacja może zostać zamieniony, być może dla implementacji makiety w testach jednostkowych.

## <a name="default-services"></a>Usługi domyślne

Domyślne usługi są automatycznie dodawane do kolekcji usług aplikacji.

| Usługa | Okres istnienia | Opis |
| ---
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---- | ---title: "ASP.NET Core Blazor wstrzykiwania zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---- | ---title: "ASP.NET Core Blazor wstrzykiwania zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | | <xref:System.Net.Http.HttpClient> | Przejściowe | Zapewnia metody wysyłania żądań HTTP i odbierania odpowiedzi HTTP z zasobu identyfikowanego przez identyfikator URI.<br><br>Wystąpienie elementu <xref:System.Net.Http.HttpClient> w aplikacji sieci Blazor webassembly używa przeglądarki do obsługi ruchu HTTP w tle.<br><br>BlazorAplikacje serwera nie domyślnie zawierają <xref:System.Net.Http.HttpClient> skonfigurowane jako usługa. Udostępnianie <xref:System.Net.Http.HttpClient> Blazor aplikacji serwerowej.<br><br>Aby uzyskać więcej informacji, zobacz <xref:blazor/call-web-api>. | | <xref:Microsoft.JSInterop.IJSRuntime> | Pojedyncze ( Blazor zestaw webassembly)<br>Zakres ( Blazor serwer) | Reprezentuje wystąpienie środowiska uruchomieniowego JavaScript, w którym są wysyłane wywołania języka JavaScript. Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>. | | <xref:Microsoft.AspNetCore.Components.NavigationManager> | Pojedyncze ( Blazor zestaw webassembly)<br>Zakres ( Blazor serwer) | Zawiera pomocników do pracy z identyfikatorami URI i stanem nawigacji. Aby uzyskać więcej informacji, zobacz [identyfikatory URI i pomocnika stanu nawigacji](xref:blazor/routing#uri-and-navigation-state-helpers). |

Niestandardowy dostawca usług nie dostarcza automatycznie usług domyślnych wymienionych w tabeli. W przypadku użycia niestandardowego dostawcy usług i wymagania usług wymienionych w tabeli należy dodać wymagane usługi do nowego dostawcy usług.

## <a name="add-services-to-an-app"></a>Dodawanie usług do aplikacji

### <a name="blazor-webassembly"></a>BlazorZestaw webassembly

Skonfiguruj usługi dla kolekcji usług aplikacji w `Main` metodzie *program.cs*. W poniższym przykładzie `MyDependency` implementacja jest zarejestrowana dla `IMyDependency` :

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

Po skompilowaniu hosta usługi można uzyskać dostęp z poziomu głównego DI zakresu przed renderowaniem wszystkich składników. Może to być przydatne do uruchamiania logiki inicjowania przed renderowaniem zawartości:

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

Host udostępnia również centralne wystąpienie konfiguracji dla aplikacji. W poprzednim przykładzie adres URL usługi Pogoda jest przesyłany z domyślnego źródła konfiguracji (na przykład *appSettings. JSON*) do `InitializeWeatherAsync` :

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

### <a name="blazor-server"></a>BlazorServer

Po utworzeniu nowej aplikacji zapoznaj się z tą `Startup.ConfigureServices` metodą:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda jest przenoszona <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> a, która jest listą obiektów deskryptora usługi ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ). Usługi są dodawane przez dostarczenie deskryptorów usługi do kolekcji usług. Poniższy przykład demonstruje koncepcję z `IDataAccess` interfejsem i jego konkretną implementacją `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Okres istnienia usługi

Usługi można skonfigurować przy użyciu okresów istnienia podanych w poniższej tabeli.

| Okres istnienia | Opis |
| ---
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---- | ---title: "ASP.NET Core Blazor wstrzykiwania zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor iniekcja zależności" autor: Opis: "Zobacz, jak Blazor aplikacje mogą wstrzyknąć usługi do składników".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor Aplikacje webassembly nie mają obecnie koncepcji DI Scopes. `Scoped`-zarejestrowane usługi zachowują się jak `Singleton` usługi. Jednak Blazor model hostingu serwera obsługuje `Scoped` okres istnienia. W przypadku Blazor aplikacji serwerowych Rejestracja usługi w zakresie jest objęta zakresem *połączenia*. Z tego powodu użycie usług objętych zakresem jest preferowane dla usług, które powinny być objęte zakresem bieżącego użytkownika, nawet jeśli bieżącym celem jest uruchomienie po stronie klienta w przeglądarce. | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI tworzy *pojedyncze wystąpienie* usługi. Wszystkie składniki wymagające `Singleton` usługi odbierają wystąpienie tej samej usługi. | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Za każdym razem, gdy składnik uzyskuje wystąpienie `Transient` usługi z kontenera usługi, otrzymuje *nowe wystąpienie* usługi. |

System DI jest oparty na systemie DI w ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Żądanie usługi w składniku

Po dodaniu usług do kolekcji usług należy wstrzyknąć usługi do składników za pomocą dyrektywy [ \@ wstrzykiwania](xref:mvc/views/razor#inject) Razor . [`@inject`](xref:mvc/views/razor#inject)ma dwa parametry:

* Typ: typ usługi do dodania.
* Property: Nazwa właściwości otrzymującej wstrzykiwaną usługę App Service. Właściwość nie wymaga ręcznego tworzenia. Kompilator tworzy właściwość.

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/dependency-injection>.

Użyj wielu [`@inject`](xref:mvc/views/razor#inject) instrukcji, aby wstrzyknąć różne usługi.

Poniższy przykład pokazuje, jak używać [`@inject`](xref:mvc/views/razor#inject) . Implementowanie usługi `Services.IDataAccess` jest wstrzykiwane do właściwości składnika `DataRepository` . Zwróć uwagę, jak kod używa tylko `IDataAccess` abstrakcji:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

Wewnętrznie wygenerowana Właściwość ( `DataRepository` ) używa [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu. Zazwyczaj ten atrybut nie jest używany bezpośrednio. Jeśli klasa podstawowa jest wymagana dla składników i właściwości wstrzykiwane są również wymagane dla klasy bazowej, należy ręcznie dodać [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
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

Złożone usługi mogą wymagać dodatkowych usług. W poprzednim przykładzie `DataAccess` może być wymagana <xref:System.Net.Http.HttpClient> Usługa domyślna. [`@inject`](xref:mvc/views/razor#inject)(lub [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybut) nie jest dostępny do użytku w usługach. Zamiast tego należy użyć *iniekcji konstruktora* . Wymagane usługi są dodawane przez dodanie parametrów do konstruktora usługi. Gdy program DI tworzy usługę, rozpoznaje usługi, których wymaga w konstruktorze i udostępnia je odpowiednio.

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

* Jeden Konstruktor musi istnieć, którego argumenty mogą być zrealizowane przez DI. Dodatkowe parametry, które nie są objęte przez DI, są dozwolone, jeśli określają wartości domyślne.
* Odpowiedni Konstruktor musi być *publiczny*.
* Musi istnieć jeden odpowiedni Konstruktor. W przypadku niejednoznaczności, polecenie DI zgłasza wyjątek.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Klasy składników podstawowych narzędzi do zarządzania DI zakresem

W przypadku aplikacji ASP.NET Core usługi o określonym zakresie są zwykle objęte zakresem bieżącego żądania. Po zakończeniu żądania wszystkie usługi w zakresie lub przejściowym są usuwane przez system DI. W obszarze Blazor aplikacje serwera zakres żądań jest używany przez czas trwania połączenia klienta, co może spowodować, że usługi przejściowe i objęte zakresem będą dużo dłużej niż oczekiwano. W Blazor aplikacjach webassembly usługi zarejestrowane w określonym okresie istnienia są traktowane jako pojedyncze, tak aby znajdowały się one dłużej niż usługi w zakresie w typowym ASP.NET Core aplikacji.

Zastosowanie tego typu w aplikacjach ogranicza okres istnienia usługi Blazor <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . <xref:Microsoft.AspNetCore.Components.OwningComponentBase>jest abstrakcyjnym typem pochodnym <xref:Microsoft.AspNetCore.Components.ComponentBase> , który tworzy zakres di odpowiadający okresowi istnienia składnika. Korzystając z tego zakresu, możliwe jest korzystanie z usługi DI Services z okresem istnienia w zakresie i posiadanie ich na żywo tak długo, jak w przypadku składnika. Gdy składnik zostanie zniszczony, usługi z dostawcy usług w zasięgu składnika również zostaną usunięte. Może to być przydatne w przypadku usług, które:

* Należy ponownie użyć w składniku, ponieważ przejściowy okres istnienia jest nieodpowiedni.
* Nie powinny być współużytkowane przez składniki, ponieważ pojedynczy okres istnienia jest nieodpowiedni.

Dostępne są dwie wersje <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typu:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase>jest abstrakcyjnym, jednorazowym elementem podrzędnym <xref:Microsoft.AspNetCore.Components.ComponentBase> typu z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> właściwością chronioną typu <xref:System.IServiceProvider> . Ten dostawca może służyć do rozpoznawania usług objętych zakresem czasu istnienia składnika.

  Program DI Services wprowadzany do składnika przy użyciu [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atrybutu lub nie jest tworzony w zakresie składnika. Aby można było użyć zakresu składnika, usługi muszą zostać rozwiązane przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> lub <xref:System.IServiceProvider.GetService%2A> . Wszystkie usługi rozpoznane przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> dostawcy mają swoje zależności z tego samego zakresu.

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>pochodzi z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i dodaje <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> Właściwość zwracającą wystąpienie `T` z dostawcy i zakresu. Ten typ jest wygodnym sposobem uzyskiwania dostępu do usług objętych zakresem bez użycia wystąpienia, <xref:System.IServiceProvider> gdy istnieje jedna usługa podstawowa wymagana przez aplikację z kontenera di używającego zakresu składnika. Ta <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> Właściwość jest dostępna, aby aplikacja mogła uzyskać usługi innych typów, w razie potrzeby.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Korzystanie z Entity Framework DbContext z elementu DI

Jednym z typowych typów usług pobieranych z aplikacji sieci Web typu "i" jest Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> . Rejestrowanie usług EF przy użyciu <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> <xref:Microsoft.EntityFrameworkCore.DbContext> Domyślnie dodaje usługę jako objętą zakresem. Rejestracja w ramach usługi w zakresie może prowadzić do problemów w Blazor aplikacjach, ponieważ powoduje to <xref:Microsoft.EntityFrameworkCore.DbContext> , że wystąpienia są długotrwałe i udostępniane w całej aplikacji. <xref:Microsoft.EntityFrameworkCore.DbContext>nie jest bezpieczne dla wątków i nie może być używane współbieżnie.

W zależności od aplikacji korzystanie z programu <xref:Microsoft.AspNetCore.Components.OwningComponentBase> w celu ograniczenia zakresu jednego <xref:Microsoft.EntityFrameworkCore.DbContext> składnika *może* rozwiązać ten problem. Jeśli składnik nie korzysta <xref:Microsoft.EntityFrameworkCore.DbContext> równolegle, wyprowadza składnik z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> i pobieranie <xref:Microsoft.EntityFrameworkCore.DbContext> z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> jest wystarczające, ponieważ gwarantuje to:

* Oddzielne składniki nie współdzielą <xref:Microsoft.EntityFrameworkCore.DbContext> .
* <xref:Microsoft.EntityFrameworkCore.DbContext>Mieszka tylko tak długo, jak i w zależności od tego, czy jest to składnik.

Jeśli pojedynczy składnik może korzystać z współbieżnie <xref:Microsoft.EntityFrameworkCore.DbContext> (na przykład za każdym razem, gdy użytkownik wybierze przycisk), nawet przy użyciu <xref:Microsoft.AspNetCore.Components.OwningComponentBase> nie należy unikać problemów z jednoczesnymi operacjami EF. W takim przypadku należy użyć innej <xref:Microsoft.EntityFrameworkCore.DbContext> dla każdej operacji logicznej EF. Użyj jednej z następujących metod:

* Utwórz <xref:Microsoft.EntityFrameworkCore.DbContext> bezpośrednio przy użyciu <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> jako argument, który można pobrać z elementu "i" jest bezpieczny wątkowo.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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

* Zarejestruj <xref:Microsoft.EntityFrameworkCore.DbContext> w kontenerze usługi z przejściowym okresem istnienia:
  * Podczas rejestrowania kontekstu użyj <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Metoda rozszerzenia przyjmuje dwa opcjonalne parametry typu <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> . Aby skorzystać z tej metody, należy `contextLifetime` podać tylko parametr <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . `optionsLifetime`może zachować wartość domyślną <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Przejściowy <xref:Microsoft.EntityFrameworkCore.DbContext> można wstrzyknąć jako normalny (przy użyciu [`@inject`](xref:mvc/views/razor#inject) ) do składników, które nie wykonują równolegle wielu operacji EF. Te, które mogą wykonywać wiele operacji EF jednocześnie, mogą zażądać oddzielnych <xref:Microsoft.EntityFrameworkCore.DbContext> obiektów dla każdej operacji równoległej przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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
