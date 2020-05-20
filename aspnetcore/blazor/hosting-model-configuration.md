---
<span data-ttu-id="bb565-101">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-103">'Blazor'</span></span>
- <span data-ttu-id="bb565-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-104">'Identity'</span></span>
- <span data-ttu-id="bb565-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-106">'Razor'</span></span>
- <span data-ttu-id="bb565-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="bb565-108">ASP.NET Core Blazor konfigurację modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="bb565-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="bb565-109">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb565-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bb565-110">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="bb565-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="bb565-111">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="bb565-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="bb565-112">Środowisko</span><span class="sxs-lookup"><span data-stu-id="bb565-112">Environment</span></span>

<span data-ttu-id="bb565-113">Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane.</span><span class="sxs-lookup"><span data-stu-id="bb565-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="bb565-114">Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="bb565-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="bb565-115">Hostowana Blazor aplikacja sieci webassembly pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="bb565-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="bb565-116">Wartość nagłówka to środowisko.</span><span class="sxs-lookup"><span data-stu-id="bb565-116">The value of the header is the environment.</span></span> <span data-ttu-id="bb565-117">Hostowana Blazor aplikacja i aplikacja serwera współużytkują to samo środowisko.</span><span class="sxs-lookup"><span data-stu-id="bb565-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="bb565-118">Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="bb565-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bb565-119">W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne.</span><span class="sxs-lookup"><span data-stu-id="bb565-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="bb565-120">Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="bb565-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="bb565-121">W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego pliku *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="bb565-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="bb565-122">Plik *Web. config* znajduje się w folderze *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="bb565-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="bb565-123">Aby użyć niestandardowego pliku *Web. config* dla usług IIS, które nie są zastępowane podczas publikowania aplikacji w folderze *publikowania* , zobacz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="bb565-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="bb565-124">Uzyskaj środowisko aplikacji w składniku, wprowadzając `IWebAssemblyHostEnvironment` i odczytując `Environment` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="bb565-124">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="bb565-125">Podczas uruchamiania program `WebAssemblyHostBuilder` ujawnia `IWebAssemblyHostEnvironment` `HostEnvironment` Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:</span><span class="sxs-lookup"><span data-stu-id="bb565-125">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="bb565-126">Następujące wygodne metody rozszerzające umożliwiają sprawdzanie bieżącego środowiska pod kątem nazw środowisk deweloperskich, produkcyjnych, tymczasowych i niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="bb565-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="bb565-127">"Isenvironment (" {Nazwa środowiska} ")</span><span class="sxs-lookup"><span data-stu-id="bb565-127">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="bb565-128">`IWebAssemblyHostEnvironment.BaseAddress`Właściwość może być używana podczas uruchamiania, gdy `NavigationManager` Usługa jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="bb565-128">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="bb565-129">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="bb565-129">Configuration</span></span>

Blazor<span data-ttu-id="bb565-130">Zestaw webassembly ładuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="bb565-130"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="bb565-131">Pliki ustawień aplikacji domyślnie:</span><span class="sxs-lookup"><span data-stu-id="bb565-131">App settings files by default:</span></span>
  * <span data-ttu-id="bb565-132">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="bb565-132">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="bb565-133">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="bb565-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="bb565-134">Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="bb565-134">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="bb565-135">Nie wszyscy dostawcy są odpowiednim rozwiązaniem dla Blazor aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="bb565-135">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="bb565-136">Wyjaśnienie, na których dostawców są obsługiwane dla Blazor zestawu webassembly, są śledzone przez [wyjaśnienie dostawców konfiguracji dla Blazor WASM (dotnet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="bb565-136">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="bb565-137">Konfiguracja w Blazor aplikacji webassembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bb565-137">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="bb565-138">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="bb565-138">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="bb565-139">Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="bb565-139">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="bb565-140">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb565-140">App settings configuration</span></span>

<span data-ttu-id="bb565-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="bb565-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="bb565-142">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="bb565-142">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="bb565-143">Konfiguracja dostawcy</span><span class="sxs-lookup"><span data-stu-id="bb565-143">Provider configuration</span></span>

<span data-ttu-id="bb565-144">Poniższy przykład używa, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> Aby podać dodatkową konfigurację:</span><span class="sxs-lookup"><span data-stu-id="bb565-144">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="bb565-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bb565-145">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="bb565-146">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="bb565-146">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="bb565-147">Aby odczytać inne pliki konfiguracji z folderu *wwwroot* do konfiguracji, użyj programu `HttpClient` w celu uzyskania zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="bb565-147">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="bb565-148">W przypadku korzystania z tego podejścia istniejąca `HttpClient` rejestracja usługi może używać lokalnego klienta utworzonego do odczytu pliku, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="bb565-148">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="bb565-149">plik *wwwroot/samochody. JSON*:</span><span class="sxs-lookup"><span data-stu-id="bb565-149">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="bb565-150">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bb565-150">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="bb565-151">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bb565-151">Authentication configuration</span></span>

<span data-ttu-id="bb565-152">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="bb565-152">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="bb565-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bb565-153">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="bb565-154">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="bb565-154">Logging configuration</span></span>

<span data-ttu-id="bb565-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="bb565-155">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="bb565-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bb565-156">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="bb565-157">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="bb565-157">Host builder configuration</span></span>

<span data-ttu-id="bb565-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bb565-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="bb565-159">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="bb565-159">Cached configuration</span></span>

<span data-ttu-id="bb565-160">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="bb565-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="bb565-161">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="bb565-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="bb565-162">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="bb565-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="bb565-163">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="bb565-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="bb565-164">Pliki *Service-Worker. js* i *Service-Worker-Assets. js* programu PWA muszą zostać ponownie skompilowane w ramach kompilacji, która sygnalizuje aplikacji w następnym trybie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="bb565-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="bb565-165">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="bb565-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="bb565-166">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="bb565-166">Logging</span></span>

<span data-ttu-id="bb565-167">Aby uzyskać informacje na temat Blazor obsługi rejestrowania zestawów webassembly, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="bb565-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="bb565-168">Server</span><span class="sxs-lookup"><span data-stu-id="bb565-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="bb565-169">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="bb565-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="bb565-170">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="bb565-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="bb565-171">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="bb565-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="bb565-172">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bb565-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="bb565-173">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="bb565-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="bb565-174">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="bb565-174">CSS class</span></span>                       | <span data-ttu-id="bb565-175">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="bb565-175">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="bb565-176">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-176">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-177">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-178">'Blazor'</span></span>
- <span data-ttu-id="bb565-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-179">'Identity'</span></span>
- <span data-ttu-id="bb565-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-181">'Razor'</span></span>
- <span data-ttu-id="bb565-182">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-183">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-183">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-184">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-185">'Blazor'</span></span>
- <span data-ttu-id="bb565-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-186">'Identity'</span></span>
- <span data-ttu-id="bb565-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-188">'Razor'</span></span>
- <span data-ttu-id="bb565-189">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-190">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-190">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-191">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-192">'Blazor'</span></span>
- <span data-ttu-id="bb565-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-193">'Identity'</span></span>
- <span data-ttu-id="bb565-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-195">'Razor'</span></span>
- <span data-ttu-id="bb565-196">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-197">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-197">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-198">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-199">'Blazor'</span></span>
- <span data-ttu-id="bb565-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-200">'Identity'</span></span>
- <span data-ttu-id="bb565-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-202">'Razor'</span></span>
- <span data-ttu-id="bb565-203">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-204">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-204">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-205">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-206">'Blazor'</span></span>
- <span data-ttu-id="bb565-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-207">'Identity'</span></span>
- <span data-ttu-id="bb565-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-209">'Razor'</span></span>
- <span data-ttu-id="bb565-210">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-211">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-211">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-213">'Blazor'</span></span>
- <span data-ttu-id="bb565-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-214">'Identity'</span></span>
- <span data-ttu-id="bb565-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-216">'Razor'</span></span>
- <span data-ttu-id="bb565-217">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-218">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-218">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-220">'Blazor'</span></span>
- <span data-ttu-id="bb565-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-221">'Identity'</span></span>
- <span data-ttu-id="bb565-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-223">'Razor'</span></span>
- <span data-ttu-id="bb565-224">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-225">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-225">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-227">'Blazor'</span></span>
- <span data-ttu-id="bb565-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-228">'Identity'</span></span>
- <span data-ttu-id="bb565-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-230">'Razor'</span></span>
- <span data-ttu-id="bb565-231">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-232">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-232">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-234">'Blazor'</span></span>
- <span data-ttu-id="bb565-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-235">'Identity'</span></span>
- <span data-ttu-id="bb565-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-237">'Razor'</span></span>
- <span data-ttu-id="bb565-238">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-239">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-239">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-241">'Blazor'</span></span>
- <span data-ttu-id="bb565-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-242">'Identity'</span></span>
- <span data-ttu-id="bb565-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-244">'Razor'</span></span>
- <span data-ttu-id="bb565-245">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-246">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-246">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-248">'Blazor'</span></span>
- <span data-ttu-id="bb565-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-249">'Identity'</span></span>
- <span data-ttu-id="bb565-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-251">'Razor'</span></span>
- <span data-ttu-id="bb565-252">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-253">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-253">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-254">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-255">'Blazor'</span></span>
- <span data-ttu-id="bb565-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-256">'Identity'</span></span>
- <span data-ttu-id="bb565-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-258">'Razor'</span></span>
- <span data-ttu-id="bb565-259">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-260">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-260">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-261">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-262">'Blazor'</span></span>
- <span data-ttu-id="bb565-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-263">'Identity'</span></span>
- <span data-ttu-id="bb565-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-265">'Razor'</span></span>
- <span data-ttu-id="bb565-266">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-266">'SignalR' uid:</span></span> 

<span data-ttu-id="bb565-267">---------------- | ---title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-267">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-268">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-269">'Blazor'</span></span>
- <span data-ttu-id="bb565-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-270">'Identity'</span></span>
- <span data-ttu-id="bb565-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-272">'Razor'</span></span>
- <span data-ttu-id="bb565-273">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-274">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-274">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-275">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-276">'Blazor'</span></span>
- <span data-ttu-id="bb565-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-277">'Identity'</span></span>
- <span data-ttu-id="bb565-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-279">'Razor'</span></span>
- <span data-ttu-id="bb565-280">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-281">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-281">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-282">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-283">'Blazor'</span></span>
- <span data-ttu-id="bb565-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-284">'Identity'</span></span>
- <span data-ttu-id="bb565-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-286">'Razor'</span></span>
- <span data-ttu-id="bb565-287">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-288">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-288">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-290">'Blazor'</span></span>
- <span data-ttu-id="bb565-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-291">'Identity'</span></span>
- <span data-ttu-id="bb565-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-293">'Razor'</span></span>
- <span data-ttu-id="bb565-294">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-295">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-295">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-297">'Blazor'</span></span>
- <span data-ttu-id="bb565-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-298">'Identity'</span></span>
- <span data-ttu-id="bb565-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-300">'Razor'</span></span>
- <span data-ttu-id="bb565-301">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-302">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-302">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-304">'Blazor'</span></span>
- <span data-ttu-id="bb565-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-305">'Identity'</span></span>
- <span data-ttu-id="bb565-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-307">'Razor'</span></span>
- <span data-ttu-id="bb565-308">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-308">'SignalR' uid:</span></span> 

<span data-ttu-id="bb565-309">--------- | | `components-reconnect-show`     | Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="bb565-309">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="bb565-310">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="bb565-310">The client is attempting to reconnect.</span></span> <span data-ttu-id="bb565-311">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="bb565-311">Show the modal.</span></span> <span data-ttu-id="bb565-312">| | `components-reconnect-hide`     | Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="bb565-312">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="bb565-313">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="bb565-313">Hide the modal.</span></span> <span data-ttu-id="bb565-314">| | `components-reconnect-failed`   | Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="bb565-314">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="bb565-315">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="bb565-315">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="bb565-316">| | `components-reconnect-rejected` | Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="bb565-316">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="bb565-317">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="bb565-317">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="bb565-318">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="bb565-318">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="bb565-319">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="bb565-319">This connection state may result when:</span></span><ul><li><span data-ttu-id="bb565-320">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="bb565-320">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="bb565-321">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bb565-321">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="bb565-322">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="bb565-322">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="bb565-323">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="bb565-323">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="bb565-324">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="bb565-324">Render mode</span></span>

Blazor<span data-ttu-id="bb565-325">Aplikacje serwera są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="bb565-325"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="bb565-326">Ta konfiguracja jest ustawiana na stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bb565-326">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="bb565-327">`RenderMode`Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="bb565-327">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="bb565-328">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="bb565-328">Is prerendered into the page.</span></span>
* <span data-ttu-id="bb565-329">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bb565-329">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="bb565-330">Opis</span><span class="sxs-lookup"><span data-stu-id="bb565-330">Description</span></span> |
| ---
<span data-ttu-id="bb565-331">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-331">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-332">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-333">'Blazor'</span></span>
- <span data-ttu-id="bb565-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-334">'Identity'</span></span>
- <span data-ttu-id="bb565-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-336">'Razor'</span></span>
- <span data-ttu-id="bb565-337">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-338">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-338">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-339">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-340">'Blazor'</span></span>
- <span data-ttu-id="bb565-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-341">'Identity'</span></span>
- <span data-ttu-id="bb565-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-343">'Razor'</span></span>
- <span data-ttu-id="bb565-344">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-345">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-345">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-346">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-347">'Blazor'</span></span>
- <span data-ttu-id="bb565-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-348">'Identity'</span></span>
- <span data-ttu-id="bb565-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-350">'Razor'</span></span>
- <span data-ttu-id="bb565-351">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-352">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-352">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-353">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-354">'Blazor'</span></span>
- <span data-ttu-id="bb565-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-355">'Identity'</span></span>
- <span data-ttu-id="bb565-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-357">'Razor'</span></span>
- <span data-ttu-id="bb565-358">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-359">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-359">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-360">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-361">'Blazor'</span></span>
- <span data-ttu-id="bb565-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-362">'Identity'</span></span>
- <span data-ttu-id="bb565-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-364">'Razor'</span></span>
- <span data-ttu-id="bb565-365">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-366">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-366">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-367">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-368">'Blazor'</span></span>
- <span data-ttu-id="bb565-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-369">'Identity'</span></span>
- <span data-ttu-id="bb565-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-371">'Razor'</span></span>
- <span data-ttu-id="bb565-372">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-372">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-373">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-373">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-374">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-375">'Blazor'</span></span>
- <span data-ttu-id="bb565-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-376">'Identity'</span></span>
- <span data-ttu-id="bb565-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-378">'Razor'</span></span>
- <span data-ttu-id="bb565-379">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-379">'SignalR' uid:</span></span> 

<span data-ttu-id="bb565-380">---------- | ---title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-380">---------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-381">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-382">'Blazor'</span></span>
- <span data-ttu-id="bb565-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-383">'Identity'</span></span>
- <span data-ttu-id="bb565-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-385">'Razor'</span></span>
- <span data-ttu-id="bb565-386">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-387">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-387">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-388">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-389">'Blazor'</span></span>
- <span data-ttu-id="bb565-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-390">'Identity'</span></span>
- <span data-ttu-id="bb565-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-392">'Razor'</span></span>
- <span data-ttu-id="bb565-393">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bb565-394">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="bb565-394">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="bb565-395">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bb565-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bb565-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bb565-396">'Blazor'</span></span>
- <span data-ttu-id="bb565-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bb565-397">'Identity'</span></span>
- <span data-ttu-id="bb565-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bb565-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="bb565-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bb565-399">'Razor'</span></span>
- <span data-ttu-id="bb565-400">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bb565-400">'SignalR' uid:</span></span> 

<span data-ttu-id="bb565-401">------ | | `ServerPrerendered` | Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="bb565-401">------ | | `ServerPrerendered` | Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb565-402">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb565-402">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="bb565-403">| | `Server`            | Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="bb565-403">| | `Server`            | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb565-404">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="bb565-404">Output from the component isn't included.</span></span> <span data-ttu-id="bb565-405">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb565-405">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="bb565-406">| | `Static`            | Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="bb565-406">| | `Static`            | Renders the component into static HTML.</span></span> |

<span data-ttu-id="bb565-407">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="bb565-407">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="bb565-408">Konfigurowanie SignalR klienta dla Blazor aplikacji serwerowych</span><span class="sxs-lookup"><span data-stu-id="bb565-408">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="bb565-409">Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="bb565-409">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="bb565-410">Na przykład możesz chcieć skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="bb565-410">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="bb565-411">Aby skonfigurować SignalR klienta w pliku *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bb565-411">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="bb565-412">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="bb565-412">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="bb565-413">Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="bb565-413">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="bb565-414">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="bb565-414">Logging</span></span>

<span data-ttu-id="bb565-415">Aby uzyskać informacje na temat Blazor obsługi rejestrowania na serwerze, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="bb565-415">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
