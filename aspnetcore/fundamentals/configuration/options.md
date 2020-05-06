---
title: Wzorzec opcji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać wzorca opcji do reprezentowania grup powiązanych ustawień w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: efce2caf37534823016c12b298afd277bab22030
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769939"
---
# <a name="options-pattern-in-aspnet-core"></a>Wzorzec opcji w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:

* [Zasady podziału interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.
* [Oddzielenie](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; ustawień związanych z różnymi częściami aplikacji nie jest zależne ani powiązane ze sobą.

Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pakiet

Pakiet [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) jest niejawnie przywoływany w aplikacjach ASP.NET Core.

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Program obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Nazwane opcje](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja do ponownego ładowania](#reload-configuration-data-with-ioptionssnapshot)
* Unieważnianie opcji selektywnych (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po wystąpieniu całej <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji. Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> program do `TOptions` buforowania wystąpień. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania. Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>program.

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.

Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów. Następująca Klasa, `MyOptions`, ma dwie właściwości, `Option1` i. `Option2` Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1`. `Option2`ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> programem w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Plik *appSettings. JSON* przykładu Określa wartości dla `option1` i: `option2`

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji `OnGet` Metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

## <a name="configure-simple-options-with-a-delegate"></a>Skonfiguruj proste opcje z delegatem

Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.

Użyj delegata, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie zostanie dodana druga <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługa do kontenera usługi. Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.

Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji. Po uruchomieniu aplikacji `OnGet` Metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.

Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji. Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.

Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]`. Na przykład `MyOptions.Option1` właściwość jest powiązana z kluczem `Option1`, który jest odczytywany z `option1` właściwości w pliku *appSettings. JSON*.

W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z sekcją `subsection` pliku *appSettings. JSON* :

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.

Plik *appSettings. JSON* przykładu definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2`:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` Klasa definiuje właściwości `SubOption1` i `SubOption2`, aby przechowywać wartości opcji (*modele/MySubOptions. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Iniekcja opcji

Iniekcja opcji jest przedstawiana jako przykład 4 w przykładowej aplikacji.

Wsuń <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:

* Widok Razor strony lub MVC z [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywą.
* Model strony lub widoku.

Poniższy przykład z przykładowej aplikacji wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu strony (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` `@inject` jak wstrzyknąć przy użyciu dyrektywy:

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot

Ponowne ładowanie danych konfiguracyjnych przy <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> użyciu programu jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.

W <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>przypadku korzystania z opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.

Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:

* `IOptionsMonitor`jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.
* `IOptionsSnapshot`jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie konstruowania `IOptionsSnapshot<T>` obiektu. Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.

W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po wprowadzeniu zmian *appSettings. JSON* (*Pages/index. cshtml. cs*). Wiele żądań do zwracanych przez serwer wartości stałych dostarczonych przez plik *appSettings. JSON* do momentu zmiany pliku i ponownego załadowania konfiguracji.

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z pliku *appSettings. JSON* :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Zmień wartości w pliku *appSettings. JSON* na `value1_from_json UPDATED` i `200`. Zapisz plik *appSettings. JSON* . Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji w programie IConfigureNamedOptions

Pomoc techniczna dotycząca opcji <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> nazwanych w programie jest prezentowana jako przykład 6 w przykładowej aplikacji.

Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji. W przykładowej aplikacji, nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która [wywołuje\<ConfigureNamedOptions TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach jest uwzględniana wielkość liter.

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Aplikacja Przykładowa uzyskuje dostęp do nazwanych <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> opcji przy użyciu (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appSettings. JSON* . `named_options_2`wartości są podawane przez:

* `named_options_2` Delegat w `ConfigureServices` dla `Option1`.
* Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.

## <a name="configure-all-options-with-the-configureall-method"></a>Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll

Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości. Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Wszystkie opcje są nazwanymi wystąpieniami. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>także. Domyślna implementacja logiki <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma na celu odpowiednie użycie. `null` Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).

## <a name="optionsbuilder-api"></a>Interfejs API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są `OptionsBuilder`dostępne tylko za pośrednictwem programu.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Korzystanie z usług DI Services w celu konfigurowania opcji

Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). `OptionsBuilder<TOptions>`zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , które zezwalają na użycie maksymalnie pięciu usług w celu skonfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.

Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane. Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora akceptującego określone typy usług ogólnych. 

## <a name="options-validation"></a>Sprawdzanie poprawności opcji

Sprawdzanie poprawności opcji umożliwia sprawdzenie opcji w przypadku skonfigurowania opcji. Wywołaj `Validate` metodę walidacji zwracającą `true` wartość, jeśli opcje są `false` prawidłowe i jeśli są nieprawidłowe:

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Poprzedni przykład ustawia nazwane wystąpienie opcji na `optionalOptionsName`. Domyślne wystąpienie opcji to `Options.DefaultName`.

Walidacja jest uruchamiana po utworzeniu wystąpienia opcji. Wystąpienie opcji gwarantuje przekazanie walidacji przy pierwszym dostępie do niego.

> [!IMPORTANT]
> Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji. Na przykład `IOptionsSnapshot` opcje są tworzone i sprawdzane jednokrotnie dla każdego żądania po pierwszym uzyskaniu dostępu do tych opcji. Te `IOptionsSnapshot` opcje nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.

`Validate` Metoda akceptuje `Func<TOptions, bool>`. Aby w pełni dostosować walidację `IValidateOptions<TOptions>`, zaimplementuj, która umożliwia:

* Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Walidacja zależna od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`weryfikuje

* Konkretne nazwane wystąpienie opcji.
* Wszystkie opcje w `name` przypadku `null`.

Zwróć `ValidateOptionsResult` z implementacji interfejsu:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Sprawdzanie poprawności opartej na adnotacji danych jest dostępne w pakiecie [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) poprzez <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> wywołanie `OptionsBuilder<TOptions>`metody w. `Microsoft.Extensions.Options.DataAnnotations`jest niejawnie przywoływany w aplikacjach ASP.NET Core.

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

Eager sprawdzanie poprawności (niepowodzenie szybkie przy uruchamianiu) jest rozważane dla przyszłej wersji.

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw wartość po konfiguracji za <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>pomocą. Po wykonaniu wszystkich <xref:Microsoft.Extensions.Options.IConfigureOptions%601> czynności konfiguracyjnych są wykonywane następujące czynności:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępny do po skonfigurowaniu nazwanych opcji:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Dostęp do opcji podczas uruchamiania

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure`, ponieważ usługi zostały skompilowane przed `Configure` wykonaniem metody.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices`. Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:

* [Zasady podziału interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.
* [Oddzielenie](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; ustawień związanych z różnymi częściami aplikacji nie jest zależne ani powiązane ze sobą.

Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Program obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Nazwane opcje](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja do ponownego ładowania](#reload-configuration-data-with-ioptionssnapshot)
* Unieważnianie opcji selektywnych (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po wystąpieniu całej <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji. Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> program do `TOptions` buforowania wystąpień. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania. Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>program.

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.

Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów. Następująca Klasa, `MyOptions`, ma dwie właściwości, `Option1` i. `Option2` Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1`. `Option2`ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> programem w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Plik *appSettings. JSON* przykładu Określa wartości dla `option1` i: `option2`

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji `OnGet` Metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

## <a name="configure-simple-options-with-a-delegate"></a>Skonfiguruj proste opcje z delegatem

Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.

Użyj delegata, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie zostanie dodana druga <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługa do kontenera usługi. Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.

Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji. Po uruchomieniu aplikacji `OnGet` Metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.

Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji. Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.

Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]`. Na przykład `MyOptions.Option1` właściwość jest powiązana z kluczem `Option1`, który jest odczytywany z `option1` właściwości w pliku *appSettings. JSON*.

W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z sekcją `subsection` pliku *appSettings. JSON* :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.

Plik *appSettings. JSON* przykładu definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` Klasa definiuje właściwości `SubOption1` i `SubOption2`, aby przechowywać wartości opcji (*modele/MySubOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Iniekcja opcji

Iniekcja opcji jest przedstawiana jako przykład 4 w przykładowej aplikacji.

Wsuń <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:

* Widok Razor strony lub MVC z [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywą.
* Model strony lub widoku.

Poniższy przykład z przykładowej aplikacji wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu strony (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` `@inject` jak wstrzyknąć przy użyciu dyrektywy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot

Ponowne ładowanie danych konfiguracyjnych przy <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> użyciu programu jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.

W <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>przypadku korzystania z opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.

Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:

* `IOptionsMonitor`jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.
* `IOptionsSnapshot`jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie konstruowania `IOptionsSnapshot<T>` obiektu. Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.

W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po wprowadzeniu zmian *appSettings. JSON* (*Pages/index. cshtml. cs*). Wiele żądań do zwracanych przez serwer wartości stałych dostarczonych przez plik *appSettings. JSON* do momentu zmiany pliku i ponownego załadowania konfiguracji.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z pliku *appSettings. JSON* :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Zmień wartości w pliku *appSettings. JSON* na `value1_from_json UPDATED` i `200`. Zapisz plik *appSettings. JSON* . Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji w programie IConfigureNamedOptions

Pomoc techniczna dotycząca opcji <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> nazwanych w programie jest prezentowana jako przykład 6 w przykładowej aplikacji.

Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji. W przykładowej aplikacji, nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która [wywołuje\<ConfigureNamedOptions TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach jest uwzględniana wielkość liter.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Aplikacja Przykładowa uzyskuje dostęp do nazwanych <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> opcji przy użyciu (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appSettings. JSON* . `named_options_2`wartości są podawane przez:

* `named_options_2` Delegat w `ConfigureServices` dla `Option1`.
* Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.

## <a name="configure-all-options-with-the-configureall-method"></a>Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll

Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości. Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Wszystkie opcje są nazwanymi wystąpieniami. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>także. Domyślna implementacja logiki <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma na celu odpowiednie użycie. `null` Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).

## <a name="optionsbuilder-api"></a>Interfejs API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są `OptionsBuilder`dostępne tylko za pośrednictwem programu.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Korzystanie z usług DI Services w celu konfigurowania opcji

Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , które pozwalają na używanie do pięciu usług do konfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.

Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane. Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora akceptującego określone typy usług ogólnych. 

## <a name="options-validation"></a>Sprawdzanie poprawności opcji

Sprawdzanie poprawności opcji umożliwia sprawdzenie opcji w przypadku skonfigurowania opcji. Wywołaj `Validate` metodę walidacji zwracającą `true` wartość, jeśli opcje są `false` prawidłowe i jeśli są nieprawidłowe:

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

Poprzedni przykład ustawia nazwane wystąpienie opcji na `optionalOptionsName`. Domyślne wystąpienie opcji to `Options.DefaultName`.

Walidacja jest uruchamiana po utworzeniu wystąpienia opcji. Wystąpienie opcji gwarantuje przekazanie walidacji przy pierwszym dostępie do niego.

> [!IMPORTANT]
> Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji. Na przykład `IOptionsSnapshot` opcje są tworzone i sprawdzane jednokrotnie dla każdego żądania po pierwszym uzyskaniu dostępu do tych opcji. Te `IOptionsSnapshot` opcje nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.

`Validate` Metoda akceptuje `Func<TOptions, bool>`. Aby w pełni dostosować walidację `IValidateOptions<TOptions>`, zaimplementuj, która umożliwia:

* Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Walidacja zależna od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`weryfikuje

* Konkretne nazwane wystąpienie opcji.
* Wszystkie opcje w `name` przypadku `null`.

Zwróć `ValidateOptionsResult` z implementacji interfejsu:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Sprawdzanie poprawności opartej na adnotacji danych jest dostępne w pakiecie [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) poprzez <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> wywołanie `OptionsBuilder<TOptions>`metody w. `Microsoft.Extensions.Options.DataAnnotations`jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

Eager sprawdzanie poprawności (niepowodzenie szybkie przy uruchamianiu) jest rozważane dla przyszłej wersji.

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw wartość po konfiguracji za <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>pomocą. Po wykonaniu wszystkich <xref:Microsoft.Extensions.Options.IConfigureOptions%601> czynności konfiguracyjnych są wykonywane następujące czynności:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępny do po skonfigurowaniu nazwanych opcji:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Dostęp do opcji podczas uruchamiania

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure`, ponieważ usługi zostały skompilowane przed `Configure` wykonaniem metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices`. Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:

* [Zasady podziału interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.
* [Oddzielenie](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; ustawień związanych z różnymi częściami aplikacji nie jest zależne ani powiązane ze sobą.

Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Program obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Nazwane opcje](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja do ponownego ładowania](#reload-configuration-data-with-ioptionssnapshot)
* Unieważnianie opcji selektywnych (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po wystąpieniu całej <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji. Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> program do `TOptions` buforowania wystąpień. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania. Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>program.

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.

Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów. Następująca Klasa, `MyOptions`, ma dwie właściwości, `Option1` i. `Option2` Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1`. `Option2`ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> programem w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Plik *appSettings. JSON* przykładu Określa wartości dla `option1` i: `option2`

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji `OnGet` Metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> W przypadku używania niestandardowej <xref:System.Configuration.ConfigurationBuilder> konfiguracji opcji do ładowania z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

## <a name="configure-simple-options-with-a-delegate"></a>Skonfiguruj proste opcje z delegatem

Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.

Użyj delegata, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie zostanie dodana druga <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługa do kontenera usługi. Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.

Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji. Po uruchomieniu aplikacji `OnGet` Metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.

Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji. Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.

Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]`. Na przykład `MyOptions.Option1` właściwość jest powiązana z kluczem `Option1`, który jest odczytywany z `option1` właściwości w pliku *appSettings. JSON*.

W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z sekcją `subsection` pliku *appSettings. JSON* :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.

Plik *appSettings. JSON* przykładu definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` Klasa definiuje właściwości `SubOption1` i `SubOption2`, aby przechowywać wartości opcji (*modele/MySubOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Gdy aplikacja jest uruchomiona, `OnGet` Metoda zwraca ciąg pokazujący wartości klasy podopcji:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Opcje udostępniane przez model widoku lub bezpośrednie iniekcja widoku

Opcje udostępniane przez model widoku lub bezpośrednie wstrzyknięcie widoku są przedstawiane jako przykład 4 w aplikacji przykładowej.

Opcje można dostarczyć w modelu widoku lub poprzez wstrzyknięcie <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bezpośrednio do widoku (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` `@inject` jak wstrzyknąć przy użyciu dyrektywy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot

Ponowne ładowanie danych konfiguracyjnych przy <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> użyciu programu jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>obsługuje opcje ponownego ładowania z minimalnym obciążeniem przetwarzania.

Opcje są obliczane raz dla żądania w przypadku uzyskiwania dostępu do pamięci podręcznej i buforowania jej przez okres istnienia żądania.

W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po wprowadzeniu zmian *appSettings. JSON* (*Pages/index. cshtml. cs*). Wiele żądań do zwracanych przez serwer wartości stałych dostarczonych przez plik *appSettings. JSON* do momentu zmiany pliku i ponownego załadowania konfiguracji.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z pliku *appSettings. JSON* :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Zmień wartości w pliku *appSettings. JSON* na `value1_from_json UPDATED` i `200`. Zapisz plik *appSettings. JSON* . Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji w programie IConfigureNamedOptions

Pomoc techniczna dotycząca opcji <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> nazwanych w programie jest prezentowana jako przykład 6 w przykładowej aplikacji.

Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji. W przykładowej aplikacji, nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która [wywołuje\<ConfigureNamedOptions TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach jest uwzględniana wielkość liter.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Aplikacja Przykładowa uzyskuje dostęp do nazwanych <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> opcji przy użyciu (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appSettings. JSON* . `named_options_2`wartości są podawane przez:

* `named_options_2` Delegat w `ConfigureServices` dla `Option1`.
* Wartość domyślna dla `Option2` dostarczonych przez `MyOptions` klasę.

## <a name="configure-all-options-with-the-configureall-method"></a>Skonfiguruj wszystkie opcje przy użyciu metody ConfigureAll

Skonfiguruj wszystkie wystąpienia opcji za pomocą <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Poniższy kod konfiguruje `Option1` wszystkie wystąpienia konfiguracji za pomocą wspólnej wartości. Ręcznie Dodaj następujący kod do `Startup.ConfigureServices` metody:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Uruchomienie przykładowej aplikacji po dodaniu kodu daje następujący wynik:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Wszystkie opcje są nazwanymi wystąpieniami. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>także. Domyślna implementacja logiki <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma na celu odpowiednie użycie. `null` Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).

## <a name="optionsbuilder-api"></a>Interfejs API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są `OptionsBuilder`dostępne tylko za pośrednictwem programu.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Korzystanie z usług DI Services w celu konfigurowania opcji

Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , które pozwalają na używanie do pięciu usług do konfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.

Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane. Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora akceptującego określone typy usług ogólnych. 

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw wartość po konfiguracji za <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>pomocą. Po wykonaniu wszystkich <xref:Microsoft.Extensions.Options.IConfigureOptions%601> czynności konfiguracyjnych są wykonywane następujące czynności:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępny do po skonfigurowaniu nazwanych opcji:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Użyj <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> , aby skonfigurować wszystkie wystąpienia konfiguracji:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Dostęp do opcji podczas uruchamiania

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure`, ponieważ usługi zostały skompilowane przed `Configure` wykonaniem metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices`. Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/index>
