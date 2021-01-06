<span data-ttu-id="a3e9f-101">Preferowanym sposobem odczytywania pokrewnych wartości konfiguracji jest użycie [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a3e9f-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a3e9f-102">Na przykład, aby odczytać następujące wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a3e9f-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="a3e9f-103">Utwórz następującą `PositionOptions` klasę:</span><span class="sxs-lookup"><span data-stu-id="a3e9f-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="a3e9f-104">Klasa opcji:</span><span class="sxs-lookup"><span data-stu-id="a3e9f-104">An options class:</span></span>

* <span data-ttu-id="a3e9f-105">Nie może być abstrakcyjny z publicznym konstruktorem bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="a3e9f-106">Wszystkie publiczne właściwości odczytu i zapisu typu są powiązane.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="a3e9f-107">Pola **nie** są powiązane.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-107">Fields are \***not** _ bound.</span></span> <span data-ttu-id="a3e9f-108">W poprzednim kodzie `Position` nie jest powiązany.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="a3e9f-109">`Position`Właściwość jest używana, więc ciąg `"Position"` nie musi być trudno zakodowany w aplikacji podczas wiązania klasy z dostawcą konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="a3e9f-110">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="a3e9f-110">The following code:</span></span>

<span data-ttu-id="a3e9f-111">_ Wywołuje [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , aby powiązać `PositionOptions` klasę z `Position` sekcją.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-111">_ Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="a3e9f-112">Wyświetla `Position` dane konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="a3e9f-113">W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="a3e9f-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a3e9f-115">`ConfigurationBinder.Get<T>` może być wygodniejsze niż używanie `ConfigurationBinder.Bind` .</span><span class="sxs-lookup"><span data-stu-id="a3e9f-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="a3e9f-116">Poniższy kod przedstawia sposób użycia `ConfigurationBinder.Get<T>` z `PositionOptions` klasą:</span><span class="sxs-lookup"><span data-stu-id="a3e9f-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="a3e9f-117">W powyższym kodzie domyślnie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="a3e9f-118">Alternatywne podejście w przypadku używania **wzorca \* opcji** _ ma wiązać się z `Position` sekcją i dodać ją do [kontenera usługi iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a3e9f-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a3e9f-119">W poniższym kodzie `PositionOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="a3e9f-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="a3e9f-120">Korzystając z powyższego kodu, poniższy kod odczytuje opcje pozycji:</span><span class="sxs-lookup"><span data-stu-id="a3e9f-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="a3e9f-121">W powyższym kodzie zmiany w pliku konfiguracji JSON po rozpoczęciu aplikacji ***nie*** są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="a3e9f-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="a3e9f-122">Aby odczytać zmiany po rozpoczęciu pracy aplikacji, użyj [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span><span class="sxs-lookup"><span data-stu-id="a3e9f-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
