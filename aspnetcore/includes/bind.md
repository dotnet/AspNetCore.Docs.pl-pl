Preferowanym sposobem odczytywania pokrewnych wartości konfiguracji jest użycie [wzorca opcji](xref:fundamentals/configuration/options). Na przykład, aby odczytać następujące wartości konfiguracji:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Utwórz następującą `PositionOptions` klasę:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Klasa opcji:

* Nie może być abstrakcyjny z publicznym konstruktorem bez parametrów.
* Wszystkie publiczne właściwości odczytu i zapisu typu są powiązane.
* Pola ***nie*** są powiązane. W poprzednim kodzie `Position` nie jest powiązany. `Position`Właściwość jest używana, więc ciąg `"Position"` nie musi być trudno zakodowany w aplikacji podczas wiązania klasy z dostawcą konfiguracji.

Następujący kod:

* Wywołuje [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , aby powiązać `PositionOptions` klasę z `Position` sekcją.
* Wyświetla `Position` dane konfiguracji.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)tworzy powiązania i zwraca określony typ. `ConfigurationBinder.Get<T>`może być wygodniejsze niż używanie `ConfigurationBinder.Bind` . Poniższy kod przedstawia sposób użycia `ConfigurationBinder.Get<T>` z `PositionOptions` klasą:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.

Alternatywne podejście w przypadku używania ***wzorca opcji*** ma na celu powiązanie `Position` sekcji i dodanie jej do [kontenera usługi iniekcji zależności](xref:fundamentals/dependency-injection). W poniższym kodzie `PositionOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Korzystając z powyższego kodu, poniższy kod odczytuje opcje pozycji:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

W powyższym kodzie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji ***nie*** są odczytywane. Aby odczytać zmiany po rozpoczęciu pracy aplikacji, użyj [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).
