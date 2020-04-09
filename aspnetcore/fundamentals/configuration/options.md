---
title: Wzorzec opcji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać wzorca opcji do reprezentowania grup powiązanych ustawień w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665460"
---
# <a name="options-pattern-in-aspnet-core"></a>Wzorzec opcji w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza do oddzielnych klas, aplikacja jest zgodna z dwiema ważnymi zasadami inżynierii oprogramowania:

* [Zasada segregacji interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które zależą od ustawień konfiguracji, zależą tylko od używanych ustawień konfiguracji.
* [Separacja dotyczy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Ustawienia dla różnych części aplikacji nie są zależne lub powiązane ze sobą.

Opcje zapewniają również mechanizm sprawdzania poprawności danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Sprawdzanie poprawności opcji.](#options-validation)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pakiet

Pakiet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) jest niejawnie odwoływany w aplikacjach ASP.NET Core.

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania `TOptions` opcjami powiadomień o wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Opcje nazwane](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja z doładowaniem](#reload-configuration-data-with-ioptionssnapshot)
* Selektywne unieważnienie<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>opcji ( )

[Scenariusze po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę <xref:Microsoft.Extensions.Options.IConfigureOptions%601> opcji po zakończeniu całej konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych opcji wystąpień. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja <xref:Microsoft.Extensions.Options.IConfigureOptions%601> przyjmuje <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> wszystkie zarejestrowane i uruchamia wszystkie konfiguracje pierwszy, a następnie po konfiguracji. Rozróżnia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> przez `TOptions` do buforowania wystąpień. Unieważnia <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> opcje wystąpień na monitorze, tak aby wartość<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>została ponownie skompensowana ( ). Wartości można wprowadzać <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>ręcznie za pomocą pliku . Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być odtworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie skomputowane na każde żądanie. Aby uzyskać więcej informacji, zobacz [reload danych konfiguracyjnych z IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) sekcji.

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Jednak <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje poprzednich scenariuszy . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> Możesz nadal używać <xref:Microsoft.Extensions.Options.IOptions%601> w istniejących strukturach i bibliotekach, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>interfejsu i nie wymagają scenariuszy dostarczonych przez program .

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest pokazana jako przykład 1 w przykładowej aplikacji.

Klasa opcji musi być nie abstrakcyjna z konstruktorem bez parametrów publicznych. Następująca klasa `MyOptions`, ma dwie `Option1` `Option2`właściwości i . Ustawienie wartości domyślnych jest opcjonalne, ale konstruktor klas `Option1`w poniższym przykładzie ustawia wartość domyślną . `Option2`ma ustawioną wartość domyślną przez zainicjowanie właściwości bezpośrednio (*Models/MyOptions.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Klasa `MyOptions` jest dodawana do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontenera usługi z konfiguracją i powiązana z nią:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora,](xref:mvc/controllers/dependency-injection) <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aby uzyskać dostęp do ustawień (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Przykładowy plik *appsettings.json* określa wartości `option1` `option2`dla i:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Podczas korzystania <xref:System.Configuration.ConfigurationBuilder> z niestandardowego ładowania konfiguracji opcji z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:
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
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>konfiguracji opcji z pliku ustawień za pośrednictwem programu .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurowanie prostych opcji za pomocą pełnomocnika

Konfigurowanie prostych opcji z pełnomocnikiem jest pokazany jako przykład 2 w przykładowej aplikacji.

Użyj pełnomocnika, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy *(Models/MyOptionsWithDelegateConfig.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> druga usługa jest dodawana do kontenera usługi. Używa delegata do skonfigurowania powiązania `MyOptionsWithDelegateConfig`z:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs:*

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępne z pakietów NuGet i są stosowane w kolejności, w jakiej są zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> dodaje usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są zarówno określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowanego delegata.

Gdy włączona jest więcej niż jedna usługa konfiguracjona, ostatnie źródło konfiguracji, określone, *wygrywa* i ustawia wartość konfiguracji. Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest pokazana jako przykład 3 w przykładowej aplikacji.

Aplikacje należy utworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klasy) w aplikacji. Części aplikacji, które wymagają wartości konfiguracji, powinny mieć dostęp tylko do wartości konfiguracji, których używają.

Gdy opcje powiązania z konfiguracją, każda właściwość w typie `property[:sub-property:]`opcji jest powiązana z kluczem konfiguracji formularza . Na przykład `MyOptions.Option1` właściwość jest powiązana z `Option1`kluczem `option1` , który jest odczytywany z właściwości w *appsettings.json*.

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> trzecia usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z `subsection` sekcją pliku *appsettings.json:*

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda `GetSection` wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obszaru nazw.

Przykładowy plik *appsettings.json* definiuje `subsection` członka z `suboption1` kluczami dla i: `suboption2`

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

Klasa `MySubOptions` definiuje właściwości `SubOption1` i `SubOption2`, aby pomieścić wartości opcji (*Models/MySubOptions.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Po uruchomieniu aplikacji `OnGet` metoda zwraca ciąg pokazujący wartości klasy suboption:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Opcje wtrysku

Opcje iniekcji jest pokazany jako przykład 4 w przykładowej aplikacji.

Wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:

* Strona Razor lub widok MVC z dyrektywą [`@inject`](xref:mvc/views/razor#inject) Razor.
* Strona lub model widoku.

Poniższy przykład z przykładowej <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikacji wstrzykuje do modelu strony *(Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` jak `@inject` wstrzyknąć z dyrektywą:

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji wartości opcji są wyświetlane na renderowanym stronie:

![Wartości opcji Option1: value1_from_json i Option2: -1 są ładowane z modelu i przez wstrzyknięcie do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Przeładuj dane konfiguracyjne za pomocą IOptionsSnapshot

Ponowne ładowanie danych <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguracyjnych za pomocą jest pokazany w przykładzie 5 w przykładzie aplikacji.

Za <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>pomocą opcji są obliczane raz na żądanie, gdy dostęp i buforowane przez cały okres istnienia żądania.

Różnica między `IOptionsMonitor` `IOptionsSnapshot` i jest to, że:

* `IOptionsMonitor`jest [usługą singleton,](xref:fundamentals/dependency-injection#singleton) która pobiera bieżące wartości opcji w dowolnym momencie, co jest szczególnie przydatne w zależnościach singleton.
* `IOptionsSnapshot`jest [usługą o określonym zakresie](xref:fundamentals/dependency-injection#scoped) i udostępnia migawkę opcji w czasie konstruowania `IOptionsSnapshot<T>` obiektu. Opcje migawki są przeznaczone do użytku z zależnościami przejściowymi i o określonym zakresie.

W poniższym przykładzie <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> pokazano, jak nowy jest tworzony po *appsettings.json* zmiany *(Pages/Index.cshtml.cs*). Wiele żądań do serwera zwraca stałe wartości dostarczone przez plik *appsettings.json,* dopóki plik nie zostanie zmieniony i konfiguracja zostanie ponownie załadowana.

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Na poniższej ilustracji przedstawiono wartości początkowe `option1` i `option2` wartości załadowane z pliku *appsettings.json:*

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Zmień wartości w pliku *appsettings.json* na `value1_from_json UPDATED` i `200`. Zapisz plik *appsettings.json.* Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji za pomocą opcji IConfigureNamedOptions

Nazwane opcje <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> obsługi z jest pokazany jako przykład 6 w przykładowej aplikacji.

Obsługa nazwanych opcji umożliwia aplikacji rozróżnianie nazwanych konfiguracji opcji. W przykładowej aplikacji nazwane opcje są deklarowane za pomocą [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), który wywołuje [ConfigureNamedOptions\<TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach rozróżniana jest wielkość liter.

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Przykładowa aplikacja uzyskuje dostęp <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> do nazwanych opcji za pomocą *(Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Uruchamianie przykładowej aplikacji, nazwane opcje są zwracane:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appsettings.json.* `named_options_2`wartości są dostarczane przez:

* Delegat `named_options_2` w `ConfigureServices` `Option1`dla .
* Wartość domyślna `Option2` dla `MyOptions` podanych przez klasę.

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurowanie wszystkich opcji za pomocą metody ConfigureAll

Skonfiguruj wszystkie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> opcje wystąpień za pomocą metody. Poniższy kod `Option1` konfiguruje dla wszystkich wystąpień konfiguracji o wspólnej wartości. Dodaj ręcznie następujący kod `Startup.ConfigureServices` do metody:

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
> Wszystkie opcje są nazwane wystąpienia. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako `Options.DefaultName` kierowanie na `string.Empty`wystąpienie, czyli . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>również implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma logiki do użycia każdego odpowiednio. Nazwana `null` opcja jest używana do kierowania wszystkich nazwanych wystąpień<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> zamiast <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> określonego nazwanego wystąpienia ( i użyć tej konwencji).

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia tworzenie nazwanych opcji, ponieważ jest to `AddOptions<TOptions>(string optionsName)` tylko pojedynczy parametr początkowego wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności `ConfigureOptions` opcji i przeciążenia, które akceptują zależności usług są dostępne tylko za pośrednictwem `OptionsBuilder`programu .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Konfigurowanie opcji za pomocą usług DI

Dostęp do innych usług można uzyskać z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegata konfiguracji, aby [skonfigurować](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [opcjach\<TOptions optionsbuilder>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). `OptionsBuilder<TOptions>`zapewnia przeciążenia [konfiguracji,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) które umożliwiają korzystanie z maksymalnie pięciu usług w celu skonfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> który implementuje lub rejestruje typ jako usługę.

Zaleca się przekazanie delegata konfiguracji do [configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)ponieważ tworzenie usługi jest bardziej złożone. Tworzenie własnego typu jest równoważne z tym, co robi struktura dla Ciebie podczas korzystania [z funkcji Konfiguruj](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowy ogólny <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora, który akceptuje typy usług ogólnych określonych. 

## <a name="options-validation"></a>Sprawdzanie poprawności opcji

Sprawdzanie poprawności opcji umożliwia sprawdzanie poprawności opcji podczas konfigurowania opcji. Wywołanie `Validate` z metody `true` sprawdzania poprawności, `false` która zwraca, jeśli opcje są prawidłowe i jeśli nie są prawidłowe:

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

W poprzednim przykładzie ustawia nazwę `optionalOptionsName`opcji wystąpienie . Domyślnym wystąpieniem `Options.DefaultName`opcji jest .

Sprawdzanie poprawności jest uruchamiane podczas tworzenia wystąpienia opcji. Wystąpienie opcji jest gwarantowane do przekazania sprawdzania poprawności przy pierwszym dostępie.

> [!IMPORTANT]
> Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji. Na przykład `IOptionsSnapshot` opcje są tworzone i sprawdzane raz na żądanie, gdy opcje są po raz pierwszy dostępne. Opcje `IOptionsSnapshot` nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.

Metoda `Validate` akceptuje . `Func<TOptions, bool>` Aby w pełni dostosować `IValidateOptions<TOptions>`walidację, zaimplementuj, co pozwala na:

* Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Sprawdzanie poprawności, które zależy od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`Sprawdza poprawność:

* Konkretne wystąpienie opcji o nazwie.
* Wszystkie opcje, gdy `name` jest `null`.

Powrót `ValidateOptionsResult` z implementacji interfejsu:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Sprawdzanie poprawności oparte na adnotacji danych jest dostępne w pakiecie [Microsoft.Extensions.Options.DataAnnotations,](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) wywołując <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metodę na `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations`niejawnie odwołuje się w ASP.NET aplikacji Core.

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

Gorliwa weryfikacja (nie szybko przy starcie) jest rozważana dla przyszłej wersji.

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw konfigurację <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>postkonfiguracj za pomocą pliku . Po zakończeniu konfiguracji <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uruchamia się po zakończeniu całej konfiguracji:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępna do konfiguracji po nazwie opcji:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Służy <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> do konfigurowania po skonfigurowaniu wszystkich wystąpień konfiguracyjnych:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Uzyskiwanie dostępu do opcji podczas uruchamiania

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> może być `Startup.Configure`używany w , `Configure` ponieważ usługi są budowane przed wykonaniem metody.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ani `Startup.ConfigureServices`nie używaj w pliku . Niespójne stan opcji może istnieć ze względu na kolejność rejestracji usługi.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza do oddzielnych klas, aplikacja jest zgodna z dwiema ważnymi zasadami inżynierii oprogramowania:

* [Zasada segregacji interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które zależą od ustawień konfiguracji, zależą tylko od używanych ustawień konfiguracji.
* [Separacja dotyczy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Ustawienia dla różnych części aplikacji nie są zależne lub powiązane ze sobą.

Opcje zapewniają również mechanizm sprawdzania poprawności danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Sprawdzanie poprawności opcji.](#options-validation)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania `TOptions` opcjami powiadomień o wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Opcje nazwane](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja z doładowaniem](#reload-configuration-data-with-ioptionssnapshot)
* Selektywne unieważnienie<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>opcji ( )

[Scenariusze po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę <xref:Microsoft.Extensions.Options.IConfigureOptions%601> opcji po zakończeniu całej konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych opcji wystąpień. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja <xref:Microsoft.Extensions.Options.IConfigureOptions%601> przyjmuje <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> wszystkie zarejestrowane i uruchamia wszystkie konfiguracje pierwszy, a następnie po konfiguracji. Rozróżnia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> przez `TOptions` do buforowania wystąpień. Unieważnia <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> opcje wystąpień na monitorze, tak aby wartość<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>została ponownie skompensowana ( ). Wartości można wprowadzać <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>ręcznie za pomocą pliku . Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być odtworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie skomputowane na każde żądanie. Aby uzyskać więcej informacji, zobacz [reload danych konfiguracyjnych z IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) sekcji.

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Jednak <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje poprzednich scenariuszy . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> Możesz nadal używać <xref:Microsoft.Extensions.Options.IOptions%601> w istniejących strukturach i bibliotekach, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>interfejsu i nie wymagają scenariuszy dostarczonych przez program .

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest pokazana jako przykład 1 w przykładowej aplikacji.

Klasa opcji musi być nie abstrakcyjna z konstruktorem bez parametrów publicznych. Następująca klasa `MyOptions`, ma dwie `Option1` `Option2`właściwości i . Ustawienie wartości domyślnych jest opcjonalne, ale konstruktor klas `Option1`w poniższym przykładzie ustawia wartość domyślną . `Option2`ma ustawioną wartość domyślną przez zainicjowanie właściwości bezpośrednio (*Models/MyOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Klasa `MyOptions` jest dodawana do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontenera usługi z konfiguracją i powiązana z nią:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora,](xref:mvc/controllers/dependency-injection) <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aby uzyskać dostęp do ustawień (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Przykładowy plik *appsettings.json* określa wartości `option1` `option2`dla i:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Podczas korzystania <xref:System.Configuration.ConfigurationBuilder> z niestandardowego ładowania konfiguracji opcji z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:
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
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>konfiguracji opcji z pliku ustawień za pośrednictwem programu .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurowanie prostych opcji za pomocą pełnomocnika

Konfigurowanie prostych opcji z pełnomocnikiem jest pokazany jako przykład 2 w przykładowej aplikacji.

Użyj pełnomocnika, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy *(Models/MyOptionsWithDelegateConfig.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> druga usługa jest dodawana do kontenera usługi. Używa delegata do skonfigurowania powiązania `MyOptionsWithDelegateConfig`z:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs:*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępne z pakietów NuGet i są stosowane w kolejności, w jakiej są zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> dodaje usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są zarówno określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowanego delegata.

Gdy włączona jest więcej niż jedna usługa konfiguracjona, ostatnie źródło konfiguracji, określone, *wygrywa* i ustawia wartość konfiguracji. Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest pokazana jako przykład 3 w przykładowej aplikacji.

Aplikacje należy utworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klasy) w aplikacji. Części aplikacji, które wymagają wartości konfiguracji, powinny mieć dostęp tylko do wartości konfiguracji, których używają.

Gdy opcje powiązania z konfiguracją, każda właściwość w typie `property[:sub-property:]`opcji jest powiązana z kluczem konfiguracji formularza . Na przykład `MyOptions.Option1` właściwość jest powiązana z `Option1`kluczem `option1` , który jest odczytywany z właściwości w *appsettings.json*.

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> trzecia usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z `subsection` sekcją pliku *appsettings.json:*

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda `GetSection` wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obszaru nazw.

Przykładowy plik *appsettings.json* definiuje `subsection` członka z `suboption1` kluczami dla i: `suboption2`

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Klasa `MySubOptions` definiuje właściwości `SubOption1` i `SubOption2`, aby pomieścić wartości opcji (*Models/MySubOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Po uruchomieniu aplikacji `OnGet` metoda zwraca ciąg pokazujący wartości klasy suboption:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Opcje wtrysku

Opcje iniekcji jest pokazany jako przykład 4 w przykładowej aplikacji.

Wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:

* Strona Razor lub widok MVC z dyrektywą [`@inject`](xref:mvc/views/razor#inject) Razor.
* Strona lub model widoku.

Poniższy przykład z przykładowej <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikacji wstrzykuje do modelu strony *(Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` jak `@inject` wstrzyknąć z dyrektywą:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji wartości opcji są wyświetlane na renderowanym stronie:

![Wartości opcji Option1: value1_from_json i Option2: -1 są ładowane z modelu i przez wstrzyknięcie do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Przeładuj dane konfiguracyjne za pomocą IOptionsSnapshot

Ponowne ładowanie danych <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguracyjnych za pomocą jest pokazany w przykładzie 5 w przykładzie aplikacji.

Za <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>pomocą opcji są obliczane raz na żądanie, gdy dostęp i buforowane przez cały okres istnienia żądania.

Różnica między `IOptionsMonitor` `IOptionsSnapshot` i jest to, że:

* `IOptionsMonitor`jest [usługą singleton,](xref:fundamentals/dependency-injection#singleton) która pobiera bieżące wartości opcji w dowolnym momencie, co jest szczególnie przydatne w zależnościach singleton.
* `IOptionsSnapshot`jest [usługą o określonym zakresie](xref:fundamentals/dependency-injection#scoped) i udostępnia migawkę opcji w czasie konstruowania `IOptionsSnapshot<T>` obiektu. Opcje migawki są przeznaczone do użytku z zależnościami przejściowymi i o określonym zakresie.

W poniższym przykładzie <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> pokazano, jak nowy jest tworzony po *appsettings.json* zmiany *(Pages/Index.cshtml.cs*). Wiele żądań do serwera zwraca stałe wartości dostarczone przez plik *appsettings.json,* dopóki plik nie zostanie zmieniony i konfiguracja zostanie ponownie załadowana.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Na poniższej ilustracji przedstawiono wartości początkowe `option1` i `option2` wartości załadowane z pliku *appsettings.json:*

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Zmień wartości w pliku *appsettings.json* na `value1_from_json UPDATED` i `200`. Zapisz plik *appsettings.json.* Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji za pomocą opcji IConfigureNamedOptions

Nazwane opcje <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> obsługi z jest pokazany jako przykład 6 w przykładowej aplikacji.

Obsługa nazwanych opcji umożliwia aplikacji rozróżnianie nazwanych konfiguracji opcji. W przykładowej aplikacji nazwane opcje są deklarowane za pomocą [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), który wywołuje [ConfigureNamedOptions\<TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach rozróżniana jest wielkość liter.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Przykładowa aplikacja uzyskuje dostęp <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> do nazwanych opcji za pomocą *(Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Uruchamianie przykładowej aplikacji, nazwane opcje są zwracane:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appsettings.json.* `named_options_2`wartości są dostarczane przez:

* Delegat `named_options_2` w `ConfigureServices` `Option1`dla .
* Wartość domyślna `Option2` dla `MyOptions` podanych przez klasę.

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurowanie wszystkich opcji za pomocą metody ConfigureAll

Skonfiguruj wszystkie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> opcje wystąpień za pomocą metody. Poniższy kod `Option1` konfiguruje dla wszystkich wystąpień konfiguracji o wspólnej wartości. Dodaj ręcznie następujący kod `Startup.ConfigureServices` do metody:

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
> Wszystkie opcje są nazwane wystąpienia. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako `Options.DefaultName` kierowanie na `string.Empty`wystąpienie, czyli . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>również implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma logiki do użycia każdego odpowiednio. Nazwana `null` opcja jest używana do kierowania wszystkich nazwanych wystąpień<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> zamiast <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> określonego nazwanego wystąpienia ( i użyć tej konwencji).

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia tworzenie nazwanych opcji, ponieważ jest to `AddOptions<TOptions>(string optionsName)` tylko pojedynczy parametr początkowego wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności `ConfigureOptions` opcji i przeciążenia, które akceptują zależności usług są dostępne tylko za pośrednictwem `OptionsBuilder`programu .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Konfigurowanie opcji za pomocą usług DI

Dostęp do innych usług można uzyskać z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegata konfiguracji, aby [skonfigurować](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [opcjach\<TOptions optionsbuilder>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [Configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) które umożliwiają korzystanie z maksymalnie pięciu usług w celu skonfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> który implementuje lub rejestruje typ jako usługę.

Zaleca się przekazanie delegata konfiguracji do [configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)ponieważ tworzenie usługi jest bardziej złożone. Tworzenie własnego typu jest równoważne z tym, co robi struktura dla Ciebie podczas korzystania [z funkcji Konfiguruj](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowy ogólny <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora, który akceptuje typy usług ogólnych określonych. 

## <a name="options-validation"></a>Sprawdzanie poprawności opcji

Sprawdzanie poprawności opcji umożliwia sprawdzanie poprawności opcji podczas konfigurowania opcji. Wywołanie `Validate` z metody `true` sprawdzania poprawności, `false` która zwraca, jeśli opcje są prawidłowe i jeśli nie są prawidłowe:

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

W poprzednim przykładzie ustawia nazwę `optionalOptionsName`opcji wystąpienie . Domyślnym wystąpieniem `Options.DefaultName`opcji jest .

Sprawdzanie poprawności jest uruchamiane podczas tworzenia wystąpienia opcji. Wystąpienie opcji jest gwarantowane do przekazania sprawdzania poprawności przy pierwszym dostępie.

> [!IMPORTANT]
> Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji. Na przykład `IOptionsSnapshot` opcje są tworzone i sprawdzane raz na żądanie, gdy opcje są po raz pierwszy dostępne. Opcje `IOptionsSnapshot` nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.

Metoda `Validate` akceptuje . `Func<TOptions, bool>` Aby w pełni dostosować `IValidateOptions<TOptions>`walidację, zaimplementuj, co pozwala na:

* Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Sprawdzanie poprawności, które zależy od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`Sprawdza poprawność:

* Konkretne wystąpienie opcji o nazwie.
* Wszystkie opcje, gdy `name` jest `null`.

Powrót `ValidateOptionsResult` z implementacji interfejsu:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Sprawdzanie poprawności oparte na adnotacji danych jest dostępne w pakiecie [Microsoft.Extensions.Options.DataAnnotations,](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) wywołując <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metodę na `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations`znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

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

Gorliwa weryfikacja (nie szybko przy starcie) jest rozważana dla przyszłej wersji.

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw konfigurację <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>postkonfiguracj za pomocą pliku . Po zakończeniu konfiguracji <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uruchamia się po zakończeniu całej konfiguracji:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępna do konfiguracji po nazwie opcji:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Służy <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> do konfigurowania po skonfigurowaniu wszystkich wystąpień konfiguracyjnych:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Uzyskiwanie dostępu do opcji podczas uruchamiania

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> może być `Startup.Configure`używany w , `Configure` ponieważ usługi są budowane przed wykonaniem metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ani `Startup.ConfigureServices`nie używaj w pliku . Niespójne stan opcji może istnieć ze względu na kolejność rejestracji usługi.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza do oddzielnych klas, aplikacja jest zgodna z dwiema ważnymi zasadami inżynierii oprogramowania:

* [Zasada segregacji interfejsu (ISP) lub](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scenariusze hermetyzacji (klasy), które zależą od ustawień konfiguracji, zależą tylko od używanych ustawień konfiguracji.
* [Separacja dotyczy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Ustawienia dla różnych części aplikacji nie są zależne lub powiązane ze sobą.

Opcje zapewniają również mechanizm sprawdzania poprawności danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Sprawdzanie poprawności opcji.](#options-validation)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania `TOptions` opcjami powiadomień o wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Opcje nazwane](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja z doładowaniem](#reload-configuration-data-with-ioptionssnapshot)
* Selektywne unieważnienie<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>opcji ( )

[Scenariusze po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę <xref:Microsoft.Extensions.Options.IConfigureOptions%601> opcji po zakończeniu całej konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych opcji wystąpień. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja <xref:Microsoft.Extensions.Options.IConfigureOptions%601> przyjmuje <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> wszystkie zarejestrowane i uruchamia wszystkie konfiguracje pierwszy, a następnie po konfiguracji. Rozróżnia <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> przez `TOptions` do buforowania wystąpień. Unieważnia <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> opcje wystąpień na monitorze, tak aby wartość<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>została ponownie skompensowana ( ). Wartości można wprowadzać <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>ręcznie za pomocą pliku . Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> jest używana, gdy wszystkie nazwane wystąpienia powinny być odtworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie skomputowane na każde żądanie. Aby uzyskać więcej informacji, zobacz [reload danych konfiguracyjnych z IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) sekcji.

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Jednak <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje poprzednich scenariuszy . <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> Możesz nadal używać <xref:Microsoft.Extensions.Options.IOptions%601> w istniejących strukturach i bibliotekach, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>interfejsu i nie wymagają scenariuszy dostarczonych przez program .

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest pokazana jako przykład 1 w przykładowej aplikacji.

Klasa opcji musi być nie abstrakcyjna z konstruktorem bez parametrów publicznych. Następująca klasa `MyOptions`, ma dwie `Option1` `Option2`właściwości i . Ustawienie wartości domyślnych jest opcjonalne, ale konstruktor klas `Option1`w poniższym przykładzie ustawia wartość domyślną . `Option2`ma ustawioną wartość domyślną przez zainicjowanie właściwości bezpośrednio (*Models/MyOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Klasa `MyOptions` jest dodawana do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontenera usługi z konfiguracją i powiązana z nią:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora,](xref:mvc/controllers/dependency-injection) <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aby uzyskać dostęp do ustawień (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Przykładowy plik *appsettings.json* określa wartości `option1` `option2`dla i:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Podczas korzystania <xref:System.Configuration.ConfigurationBuilder> z niestandardowego ładowania konfiguracji opcji z pliku ustawień upewnij się, że ścieżka bazowa jest ustawiona poprawnie:
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
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>konfiguracji opcji z pliku ustawień za pośrednictwem programu .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurowanie prostych opcji za pomocą pełnomocnika

Konfigurowanie prostych opcji z pełnomocnikiem jest pokazany jako przykład 2 w przykładowej aplikacji.

Użyj pełnomocnika, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy *(Models/MyOptionsWithDelegateConfig.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> druga usługa jest dodawana do kontenera usługi. Używa delegata do skonfigurowania powiązania `MyOptionsWithDelegateConfig`z:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs:*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępne z pakietów NuGet i są stosowane w kolejności, w jakiej są zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> dodaje usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są zarówno określone w *appsettings.json*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowanego delegata.

Gdy włączona jest więcej niż jedna usługa konfiguracjona, ostatnie źródło konfiguracji, określone, *wygrywa* i ustawia wartość konfiguracji. Po uruchomieniu aplikacji `OnGet` metoda modelu strony zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest pokazana jako przykład 3 w przykładowej aplikacji.

Aplikacje należy utworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klasy) w aplikacji. Części aplikacji, które wymagają wartości konfiguracji, powinny mieć dostęp tylko do wartości konfiguracji, których używają.

Gdy opcje powiązania z konfiguracją, każda właściwość w typie `property[:sub-property:]`opcji jest powiązana z kluczem konfiguracji formularza . Na przykład `MyOptions.Option1` właściwość jest powiązana z `Option1`kluczem `option1` , który jest odczytywany z właściwości w *appsettings.json*.

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> trzecia usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z `subsection` sekcją pliku *appsettings.json:*

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda `GetSection` wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obszaru nazw.

Przykładowy plik *appsettings.json* definiuje `subsection` członka z `suboption1` kluczami dla i: `suboption2`

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Klasa `MySubOptions` definiuje właściwości `SubOption1` i `SubOption2`, aby pomieścić wartości opcji (*Models/MySubOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu strony zwraca ciąg z wartościami opcji (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Po uruchomieniu aplikacji `OnGet` metoda zwraca ciąg pokazujący wartości klasy suboption:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Opcje dostępne w modelu widoku lub z bezpośrednim wtryskiem widoku

Opcje dostępne w modelu widoku lub z bezpośrednim iniekcją widoku są przedstawione jako przykład 4 w przykładowej aplikacji.

Opcje mogą być dostarczane w modelu <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> widoku lub wstrzykując bezpośrednio do widoku (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Przykładowa aplikacja pokazuje, `IOptionsMonitor<MyOptions>` jak `@inject` wstrzyknąć z dyrektywą:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji wartości opcji są wyświetlane na renderowanym stronie:

![Wartości opcji Option1: value1_from_json i Option2: -1 są ładowane z modelu i przez wstrzyknięcie do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Przeładuj dane konfiguracyjne za pomocą IOptionsSnapshot

Ponowne ładowanie danych <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguracyjnych za pomocą jest pokazany w przykładzie 5 w przykładzie aplikacji.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>obsługuje opcje przeładowywania przy minimalnym obciążeniu przetwarzania.

Opcje są obliczane raz na żądanie, gdy dostęp i buforowane przez cały okres istnienia żądania.

W poniższym przykładzie <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> pokazano, jak nowy jest tworzony po *appsettings.json* zmiany *(Pages/Index.cshtml.cs*). Wiele żądań do serwera zwraca stałe wartości dostarczone przez plik *appsettings.json,* dopóki plik nie zostanie zmieniony i konfiguracja zostanie ponownie załadowana.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Na poniższej ilustracji przedstawiono wartości początkowe `option1` i `option2` wartości załadowane z pliku *appsettings.json:*

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Zmień wartości w pliku *appsettings.json* na `value1_from_json UPDATED` i `200`. Zapisz plik *appsettings.json.* Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji za pomocą opcji IConfigureNamedOptions

Nazwane opcje <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> obsługi z jest pokazany jako przykład 6 w przykładowej aplikacji.

Obsługa nazwanych opcji umożliwia aplikacji rozróżnianie nazwanych konfiguracji opcji. W przykładowej aplikacji nazwane opcje są deklarowane za pomocą [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), który wywołuje [ConfigureNamedOptions\<TOptions>. Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach rozróżniana jest wielkość liter.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Przykładowa aplikacja uzyskuje dostęp <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> do nazwanych opcji za pomocą *(Pages/Index.cshtml.cs):*

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Uruchamianie przykładowej aplikacji, nazwane opcje są zwracane:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appsettings.json.* `named_options_2`wartości są dostarczane przez:

* Delegat `named_options_2` w `ConfigureServices` `Option1`dla .
* Wartość domyślna `Option2` dla `MyOptions` podanych przez klasę.

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurowanie wszystkich opcji za pomocą metody ConfigureAll

Skonfiguruj wszystkie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> opcje wystąpień za pomocą metody. Poniższy kod `Option1` konfiguruje dla wszystkich wystąpień konfiguracji o wspólnej wartości. Dodaj ręcznie następujący kod `Startup.ConfigureServices` do metody:

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
> Wszystkie opcje są nazwane wystąpienia. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako `Options.DefaultName` kierowanie na `string.Empty`wystąpienie, czyli . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>również implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> ma logiki do użycia każdego odpowiednio. Nazwana `null` opcja jest używana do kierowania wszystkich nazwanych wystąpień<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> zamiast <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> określonego nazwanego wystąpienia ( i użyć tej konwencji).

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia tworzenie nazwanych opcji, ponieważ jest to `AddOptions<TOptions>(string optionsName)` tylko pojedynczy parametr początkowego wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności `ConfigureOptions` opcji i przeciążenia, które akceptują zależności usług są dostępne tylko za pośrednictwem `OptionsBuilder`programu .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Konfigurowanie opcji za pomocą usług DI

Dostęp do innych usług można uzyskać z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegata konfiguracji, aby [skonfigurować](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [opcjach\<TOptions optionsbuilder>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [Configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) które umożliwiają korzystanie z maksymalnie pięciu usług w celu skonfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> który implementuje lub rejestruje typ jako usługę.

Zaleca się przekazanie delegata konfiguracji do [configure,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)ponieważ tworzenie usługi jest bardziej złożone. Tworzenie własnego typu jest równoważne z tym, co robi struktura dla Ciebie podczas korzystania [z funkcji Konfiguruj](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowy ogólny <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, który ma konstruktora, który akceptuje typy usług ogólnych określonych. 

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw konfigurację <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>postkonfiguracj za pomocą pliku . Po zakończeniu konfiguracji <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uruchamia się po zakończeniu całej konfiguracji:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>jest dostępna do konfiguracji po nazwie opcji:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Służy <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> do konfigurowania po skonfigurowaniu wszystkich wystąpień konfiguracyjnych:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Uzyskiwanie dostępu do opcji podczas uruchamiania

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> może być `Startup.Configure`używany w , `Configure` ponieważ usługi są budowane przed wykonaniem metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ani `Startup.ConfigureServices`nie używaj w pliku . Niespójne stan opcji może istnieć ze względu na kolejność rejestracji usługi.

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/index>
