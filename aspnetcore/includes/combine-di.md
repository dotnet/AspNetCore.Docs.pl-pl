<a name="csc"></a>

Należy wziąć pod uwagę następującą `ConfigureServices` metodę, która rejestruje usługi i konfiguruje opcje:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Powiązane grupy rejestracji można przenieść do metody rozszerzenia w celu zarejestrowania usług. Na przykład usługi konfiguracji są dodawane do następującej klasy:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Pozostałe usługi są zarejestrowane w podobnej klasie. W poniższej `ConfigureServices` metodzie zarejestrowano usługi przy użyciu nowych metod rozszerzających:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Uwaga:_** Każda `services.Add{GROUP_NAME}` Metoda rozszerzania dodaje i potencjalnie konfiguruje usługi. Na przykład <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> dodaje kontrolery MVC usług z wymaganymi widokami i <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> dodaje usługi, Razor Pages wymaga. Zalecamy, aby aplikacje były zgodne z tą konwencją nazewnictwa. Umieść metody rozszerzające w <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> przestrzeni nazw, aby hermetyzować grupy rejestracji usług.
