<a name="csc"></a>

## <a name="combining-service-collection"></a>Łączenie kolekcji usług

Należy wziąć pod uwagę następujące kwestie `ConfigureServices` , które zawierają kilka kolekcji usług:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Powiązane grupy rejestracji można przenieść do metody rozszerzenia w celu zarejestrowania usług. Na przykład usługi konfiguracji są dodawane do następującej klasy:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Pozostałe usługi są zarejestrowane w podobnej klasie. Poniżej przedstawiono `ConfigureServices` nowe metody rozszerzenia do rejestracji usług:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Uwaga:*** Każda `services.Add{SERVICE_NAME}` Metoda rozszerzania dodaje i potencjalnie konfiguruje usługi. Na przykład <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> dodaje kontrolery MVC usług z wymaganymi widokami. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>dodaje usługi, Razor Pages wymagają. Zalecamy, aby aplikacje były zgodne z tą konwencją nazewnictwa. Umieść metody rozszerzające w przestrzeni nazw [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) , aby hermetyzować grupy rejestracji usług.