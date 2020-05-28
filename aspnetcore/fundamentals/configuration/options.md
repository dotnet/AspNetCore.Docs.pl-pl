---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--- 
# <a name="options-pattern-in-aspnet-core"></a>Wzorzec opcji w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Kirka Larkin](https://twitter.com/serpent5) i [Rick Anderson](https://twitter.com/RickAndMSFT).

Wzorzec opcji używa klas, aby zapewnić silnie określony dostęp do grup powiązanych ustawień. Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:

* [Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.
* [Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.

Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a>Powiąż hierarchiczną konfigurację

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptions%601>:

* Nie ***obsługuje:***
  * Odczytywanie danych konfiguracji po rozpoczęciu aplikacji.
  * [Nazwane opcje](#named)
* Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:

* Jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania. Aby uzyskać więcej informacji, zobacz [Użyj IOptionsSnapshot do odczytu zaktualizowanych danych](#ios).
* Jest zarejestrowany jako [zakres](xref:fundamentals/dependency-injection#scoped) i w związku z tym nie można go wstrzyknąć do pojedynczej usługi.
* Obsługuje [nazwane opcje](#named)

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:

* Służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach.
* Jest zarejestrowany jako [pojedynczy](xref:fundamentals/dependency-injection#singleton) i można go wstrzyknąć w dowolnym [okresie istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).
* Uguje
  * Powiadomienia o zmianie
  * [Nazwane opcje](#named-options-support-with-iconfigurenamedoptions)
  * [Konfiguracja do ponownego ładowania](#ios)
  * Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )
  
Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawienie lub zmianę opcji po wystąpieniu całej <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji. Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a>Użyj IOptionsSnapshot do odczytu zaktualizowanych danych

W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania. Zmiany w konfiguracji są odczytywane po uruchomieniu aplikacji podczas korzystania z dostawców konfiguracji, którzy obsługują odczytywanie zaktualizowanych wartości konfiguracji.

Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:

* `IOptionsMonitor`jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.
* `IOptionsSnapshot`jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu. Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.

Poniższy kod używa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

W poprzednim kodzie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.

## <a name="ioptionsmonitor"></a>IOptionsMonitor

Poniższy kod rejestruje wystąpienie konfiguracji, które `MyOptions` wiąże się z.

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

W poniższym przykładzie zastosowano <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a>Obsługa nazwanych opcji przy użyciu IConfigureNamedOptions

Nazwane opcje:

* Są przydatne, gdy wiele sekcji konfiguracji wiąże się z tymi samymi właściwościami.
* Uwzględnia wielkość liter.

Rozważmy następujący plik *appSettings. JSON* :

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

Zamiast tworzyć dwie klasy do powiązania `TopItem:Month` i `TopItem:Year` , dla każdej sekcji jest używana następująca Klasa:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

Poniższy kod umożliwia skonfigurowanie nazwanych opcji:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

Poniższy kod wyświetla nazwane opcje:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

Wszystkie opcje są nazwanymi wystąpieniami. <xref:Microsoft.Extensions.Options.IConfigureOptions%601>wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie. `null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Użyj tej Konwencji.

## <a name="optionsbuilder-api"></a>Interfejs API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .

`OptionsBuilder`jest używany w sekcji [Walidacja opcji](#val) .

## <a name="use-di-services-to-configure-options"></a>Korzystanie z usług DI Services w celu konfigurowania opcji

Usługi są dostępne z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). `OptionsBuilder<TOptions>`zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , które umożliwiają korzystanie z maksymalnie pięciu usług w celu konfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.

Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane. Tworzenie typu jest równoznaczne z tym, co platforma wykonuje podczas wywoływania [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych. 

<a name="val"></a>

## <a name="options-validation"></a>Sprawdzanie poprawności opcji

Sprawdzanie poprawności opcji umożliwia zweryfikowanie wartości opcji.

Rozważmy następujący plik *appSettings. JSON* :

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

Następująca Klasa wiąże się z `"MyConfig"` sekcją konfiguracji i stosuje kilka `DataAnnotations` reguł:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

Poniższy kod wywołuje <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> , aby uzyskać [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , który wiąże się z `MyConfigOptions` klasą i umożliwia `DataAnnotations` weryfikację:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

Poniższy kod wyświetla wartości konfiguracyjne lub błędy walidacji:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

Poniższy kod stosuje bardziej złożoną regułę walidacji przy użyciu delegata:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a>IValidateOptions na potrzeby złożonej walidacji

Następująca Klasa implementuje <xref:Microsoft.Extensions.Options.IValidateOptions`1> :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

`IValidateOptions`umożliwia przeniesienie kodu weryfikacyjnego z `StartUp` i do klasy.

Przy użyciu powyższego kodu sprawdzanie poprawności jest włączane w programie `Startup.ConfigureServices` przy użyciu następującego kodu:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:

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

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` . Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.

## <a name="optionsconfigurationextensions-nuget-package"></a>Options. ConfigurationExtensions — pakiet NuGet

Pakiet [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) jest niejawnie przywoływany w aplikacjach ASP.NET Core.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:

* [Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.
* [Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.

Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Program obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Nazwane opcje](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja do ponownego ładowania](#reload-configuration-data-with-ioptionssnapshot)
* Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )

Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji. Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania. Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> . Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.

Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów. Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` . Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` . `Option2`ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Plik *appSettings. JSON* przykładu Określa wartości dla `option1` i `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:

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
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

## <a name="configure-simple-options-with-a-delegate"></a>Skonfiguruj proste opcje z delegatem

Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.

Użyj delegata, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi. Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.

Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji. Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.

Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji. Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.

Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` . Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w pliku *appSettings. JSON*.

W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z sekcją `subsection` pliku *appSettings. JSON* :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.

Plik *appSettings. JSON* przykładu definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji (*modele/MySubOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):

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

* RazorWidok strony lub MVC z [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywą.
* Model strony lub widoku.

Poniższy przykład z przykładowej aplikacji wstrzyknąć <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu strony (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot

Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.

W przypadku korzystania z <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> opcji Opcje są obliczane raz dla każdego żądania, gdy jest on używany i buforowany przez okres istnienia żądania.

Różnica między `IOptionsMonitor` i `IOptionsSnapshot` to:

* `IOptionsMonitor`jest [usługą singleton](xref:fundamentals/dependency-injection#singleton) , która pobiera bieżące wartości opcji w dowolnym momencie, która jest szczególnie przydatna w pojedynczych zależnościach.
* `IOptionsSnapshot`jest [usługą objętą zakresem](xref:fundamentals/dependency-injection#scoped) i zawiera migawkę opcji w czasie `IOptionsSnapshot<T>` konstruowania obiektu. Migawki opcji są przeznaczone do użycia z zależnościami przejściowymi i zakresowymi.

W poniższym przykładzie pokazano, jak nowa <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest tworzona po wprowadzeniu zmian *appSettings. JSON* (*Pages/index. cshtml. cs*). Wiele żądań do zwracanych przez serwer wartości stałych dostarczonych przez plik *appSettings. JSON* do momentu zmiany pliku i ponownego załadowania konfiguracji.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Na poniższej ilustracji przedstawiono początkowe `option1` i `option2` wartości załadowane z pliku *appSettings. JSON* :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Zmień wartości w pliku *appSettings. JSON* na `value1_from_json UPDATED` i `200` . Zapisz plik *appSettings. JSON* . Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji w programie IConfigureNamedOptions

Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.

Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji. W aplikacji przykładowej nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach jest uwzględniana wielkość liter.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appSettings. JSON* . `named_options_2`wartości są podawane przez:

* `named_options_2`Delegat w `ConfigureServices` dla `Option1` .
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
> Wszystkie opcje są nazwanymi wystąpieniami. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie. `null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).

## <a name="optionsbuilder-api"></a>Interfejs API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Korzystanie z usług DI Services w celu konfigurowania opcji

Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.

Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane. Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych. 

## <a name="options-validation"></a>Sprawdzanie poprawności opcji

Sprawdzanie poprawności opcji umożliwia sprawdzenie opcji w przypadku skonfigurowania opcji. Wywołaj `Validate` metodę walidacji zwracającą wartość, `true` Jeśli opcje są prawidłowe i `false` Jeśli są nieprawidłowe:

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

Poprzedni przykład ustawia nazwane wystąpienie opcji na `optionalOptionsName` . Domyślne wystąpienie opcji to `Options.DefaultName` .

Walidacja jest uruchamiana po utworzeniu wystąpienia opcji. Wystąpienie opcji gwarantuje przekazanie walidacji przy pierwszym dostępie do niego.

> [!IMPORTANT]
> Sprawdzanie poprawności opcji nie chroni przed modyfikacjami opcji po utworzeniu wystąpienia opcji. Na przykład `IOptionsSnapshot` Opcje są tworzone i sprawdzane jednokrotnie dla każdego żądania po pierwszym uzyskaniu dostępu do tych opcji. Te `IOptionsSnapshot` Opcje nie są ponownie weryfikowane przy kolejnych próbach dostępu *dla tego samego żądania*.

`Validate`Metoda akceptuje `Func<TOptions, bool>` . Aby w pełni dostosować walidację, zaimplementuj `IValidateOptions<TOptions>` , która umożliwia:

* Sprawdzanie poprawności wielu typów opcji:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Walidacja zależna od innego typu opcji:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`weryfikuje

* Konkretne nazwane wystąpienie opcji.
* Wszystkie opcje w `name` przypadku `null` .

Zwróć `ValidateOptionsResult` z implementacji interfejsu:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Sprawdzanie poprawności opartej na adnotacji danych jest dostępne w pakiecie [Microsoft. Extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) poprzez wywołanie <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody w `OptionsBuilder<TOptions>` . `Microsoft.Extensions.Options.DataAnnotations`jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

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

Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:

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

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` . Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień. Gdy [Ustawienia konfiguracji](xref:fundamentals/configuration/index) są izolowane według scenariusza w osobnych klasach, aplikacja będzie zgodna z dwoma ważnymi zasadami inżynierii oprogramowania:

* [Zasada segregowania interfejsu (ISP) lub hermetyzacja](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scenariusze (klasy), które są zależne od ustawień konfiguracji, zależą tylko od ustawień konfiguracji, których używają.
* [Separacja obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): ustawienia dla różnych części aplikacji nie są zależne ani powiązane ze sobą.

Opcje umożliwiają również mechanizm weryfikacji danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz sekcję [Opcje walidacji](#options-validation) .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Interfejsy opcji

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>służy do pobierania opcji i zarządzania powiadomieniami o `TOptions` wystąpieniach. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Program obsługuje następujące scenariusze:

* Powiadomienia o zmianie
* [Nazwane opcje](#named-options-support-with-iconfigurenamedoptions)
* [Konfiguracja do ponownego ładowania](#reload-configuration-data-with-ioptionssnapshot)
* Unieważnianie opcji selektywnych ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )

Scenariusze [po konfiguracji](#options-post-configuration) umożliwiają ustawianie lub zmienianie opcji po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpieniu całej konfiguracji.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>jest odpowiedzialny za tworzenie nowych wystąpień opcji. Ma jedną <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodę. Domyślna implementacja przyjmuje wszystkie zarejestrowane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> uruchamia najpierw wszystkie konfiguracje, po których następuje po zakończeniu konfiguracji. Odróżnia między <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i <xref:Microsoft.Extensions.Options.IConfigureOptions%601> i tylko wywołuje odpowiedni interfejs.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>jest używany przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do buforowania `TOptions` wystąpień. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Unieważnia wystąpienia opcji w monitorze, aby wartość była ponownie obliczana ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Wartości można wprowadzać ręcznie przy użyciu <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda jest używana, gdy wszystkie nazwane wystąpienia powinny być ponownie tworzone na żądanie.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>jest przydatne w scenariuszach, w których opcje powinny być ponownie obliczane dla każdego żądania. Aby uzyskać więcej informacji, zobacz sekcję [Załaduj dane konfiguracji za pomocą IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>może służyć do obsługi opcji. Program <xref:Microsoft.Extensions.Options.IOptions%601> nie obsługuje jednak powyższych scenariuszy <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> . Można nadal korzystać <xref:Microsoft.Extensions.Options.IOptions%601> z istniejących platform i bibliotek, które już korzystają z <xref:Microsoft.Extensions.Options.IOptions%601> interfejsu i nie wymagają scenariuszy udostępnianych przez program <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .

## <a name="general-options-configuration"></a>Konfiguracja opcji ogólnych

Konfiguracja opcji ogólnych jest przedstawiona jako przykład 1 w aplikacji przykładowej.

Klasa Options musi być nieabstrakcyjna z publicznym konstruktorem bez parametrów. Następująca Klasa, `MyOptions` , ma dwie właściwości, `Option1` i `Option2` . Ustawienie wartości domyślnych jest opcjonalne, ale Konstruktor klasy w poniższym przykładzie ustawia wartość domyślną `Option1` . `Option2`ma ustawioną wartość domyślną, inicjując właściwość bezpośrednio (*modele/opcje. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions`Klasa jest dodawana do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Poniższy model strony używa [iniekcji zależności konstruktora](xref:mvc/controllers/dependency-injection) z programem <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w celu uzyskania dostępu do ustawień (*stron/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Plik *appSettings. JSON* przykładu Określa wartości dla `option1` i `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:

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
> Jawne ustawienie ścieżki podstawowej nie jest wymagane podczas ładowania konfiguracji opcji z pliku ustawień za pośrednictwem <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

## <a name="configure-simple-options-with-a-delegate"></a>Skonfiguruj proste opcje z delegatem

Konfigurowanie prostych opcji z delegatem jest zademonstrowane jako przykład 2 w przykładowej aplikacji.

Użyj delegata, aby ustawić wartości opcji. Przykładowa aplikacja używa `MyOptionsWithDelegateConfig` klasy (*models/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> zostanie dodana druga usługa do kontenera usługi. Używa delegata do konfigurowania powiązania z `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Można dodać wielu dostawców konfiguracji. Dostawcy konfiguracji są dostępni z pakietów NuGet i są stosowane w kolejności, w jakiej zostały zarejestrowane. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

Każde wywołanie <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> dodaje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> usługę do kontenera usługi. W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON*, ale wartości `Option1` i `Option2` są zastępowane przez skonfigurowany delegat.

Gdy jest włączona więcej niż jedna usługa konfiguracji, ostatnie Źródło konfiguracji określiło *serwer WINS* i ustawi wartość konfiguracji. Po uruchomieniu aplikacji Metoda modelu strony `OnGet` zwraca ciąg pokazujący wartości klasy opcji:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfiguracja podopcji

Konfiguracja podopcji jest przedstawiana jako przykład 3 w przykładowej aplikacji.

Aplikacje powinny tworzyć klasy opcji, które odnoszą się do określonych grup scenariuszy (klas) w aplikacji. Części aplikacji, które wymagają wartości konfiguracyjnych, powinny mieć dostęp tylko do wartości konfiguracyjnych, z których korzystają.

Po powiązaniu opcji powiązań z konfiguracją Każda właściwość w typie opcji jest powiązana z kluczem konfiguracji formularza `property[:sub-property:]` . Na przykład `MyOptions.Option1` Właściwość jest powiązana z kluczem `Option1` , który jest odczytywany z `option1` właściwości w pliku *appSettings. JSON*.

W poniższym kodzie, trzecia <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi. Wiąże `MySubOptions` się z sekcją `subsection` pliku *appSettings. JSON* :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection`Metoda wymaga <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> przestrzeni nazw.

Plik *appSettings. JSON* przykładu definiuje `subsection` element członkowski z kluczami dla `suboption1` i `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions`Klasa definiuje właściwości `SubOption1` i `SubOption2` , aby przechowywać wartości opcji (*modele/MySubOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Metoda modelu strony `OnGet` zwraca ciąg z wartościami opcji (*Pages/index. cshtml. cs*):

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

Przykładowa aplikacja pokazuje, jak wstrzyknąć `IOptionsMonitor<MyOptions>` przy użyciu `@inject` dyrektywy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po uruchomieniu aplikacji na renderowanej stronie są wyświetlane wartości opcji:

![Wartości opcji opcja1: value1_from_json i Opcja2:-1 są ładowane z modelu i przez iniekcję do widoku.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Załaduj ponownie dane konfiguracji za pomocą IOptionsSnapshot

Ponowne ładowanie danych konfiguracyjnych przy użyciu programu <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> jest zademonstrowane w przykładzie 5 w przykładowej aplikacji.

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

Zmień wartości w pliku *appSettings. JSON* na `value1_from_json UPDATED` i `200` . Zapisz plik *appSettings. JSON* . Odśwież przeglądarkę, aby zobaczyć, że wartości opcji są aktualizowane:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Obsługa nazwanych opcji w programie IConfigureNamedOptions

Pomoc techniczna dotycząca opcji nazwanych w programie <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> jest prezentowana jako przykład 6 w przykładowej aplikacji.

Obsługa "nazwanych opcji" pozwala aplikacji rozróżnić między nazwanymi konfiguracjami opcji. W aplikacji przykładowej nazwane opcje są zadeklarowane za pomocą [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), która wywołuje [ConfigureNamedOptions \<TOptions> . Skonfiguruj](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodę rozszerzenia. W nazwanych opcjach jest uwzględniana wielkość liter.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Aplikacja Przykładowa uzyskuje dostęp do nazwanych opcji przy użyciu <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*strony/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Po uruchomieniu aplikacji przykładowej są zwracane nazwane opcje:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`wartości są dostarczane z konfiguracji, które są ładowane z pliku *appSettings. JSON* . `named_options_2`wartości są podawane przez:

* `named_options_2`Delegat w `ConfigureServices` dla `Option1` .
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
> Wszystkie opcje są nazwanymi wystąpieniami. Istniejące <xref:Microsoft.Extensions.Options.IConfigureOptions%601> wystąpienia są traktowane jako docelowe dla `Options.DefaultName` wystąpienia, czyli `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>implementuje także <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiki ma na celu odpowiednie użycie. `null`Nazwana opcja służy do określania wszystkich wystąpień nazwanych zamiast określonego wystąpienia nazwanego ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> i <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> używania tej Konwencji).

## <a name="optionsbuilder-api"></a>Interfejs API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>służy do konfigurowania `TOptions` wystąpień. `OptionsBuilder`usprawnia Tworzenie nazwanych opcji, ponieważ jest tylko pojedynczym parametrem do początkowego `AddOptions<TOptions>(string optionsName)` wywołania, a nie pojawia się we wszystkich kolejnych wywołaniach. Sprawdzanie poprawności opcji i `ConfigureOptions` przeciążenia, które akceptują zależności usługi, są dostępne tylko za pośrednictwem programu `OptionsBuilder` .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Korzystanie z usług DI Services w celu konfigurowania opcji

Można uzyskać dostęp do innych usług z iniekcji zależności podczas konfigurowania opcji na dwa sposoby:

* Przekaż delegat konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) w usłudze [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) zapewnia przeciążenia [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) umożliwiające użycie maksymalnie pięciu usług w celu skonfigurowania opcji:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Utwórz własny typ, który implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> lub <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> i rejestruje typ jako usługę.

Zalecamy przekazanie delegata konfiguracji w celu [skonfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), ponieważ tworzenie usługi jest bardziej skomplikowane. Tworzenie własnego typu jest równoznaczne z tym, co jest używane przez platformę podczas [konfigurowania](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Wywołanie [konfiguracji](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) rejestruje przejściowe ogólne <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , który ma konstruktora akceptującego określone typy usług ogólnych. 

## <a name="options-post-configuration"></a>Opcje po konfiguracji

Ustaw wartość po konfiguracji za pomocą <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Po wykonaniu wszystkich czynności konfiguracyjnych są wykonywane <xref:Microsoft.Extensions.Options.IConfigureOptions%601> następujące czynności:

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

<xref:Microsoft.Extensions.Options.IOptions%601>i <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> mogą być używane w `Startup.Configure` , ponieważ usługi zostały skompilowane przed wykonaniem `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nie używaj <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> w `Startup.ConfigureServices` . Niespójny stan opcji może istnieć ze względu na kolejność rejestracji usług.

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/index>
