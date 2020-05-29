---
<span data-ttu-id="39816-101">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-103">'Blazor'</span></span>
- <span data-ttu-id="39816-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-104">'Identity'</span></span>
- <span data-ttu-id="39816-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-106">'Razor'</span></span>
- <span data-ttu-id="39816-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="39816-108">ASP.NET Core Blazor konfigurację modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="39816-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="39816-109">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="39816-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="39816-110">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="39816-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="39816-111">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="39816-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="39816-112">Środowisko</span><span class="sxs-lookup"><span data-stu-id="39816-112">Environment</span></span>

<span data-ttu-id="39816-113">Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane.</span><span class="sxs-lookup"><span data-stu-id="39816-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="39816-114">Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="39816-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="39816-115">Hostowana Blazor aplikacja sieci webassembly pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="39816-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="39816-116">Wartość nagłówka to środowisko.</span><span class="sxs-lookup"><span data-stu-id="39816-116">The value of the header is the environment.</span></span> <span data-ttu-id="39816-117">Hostowana Blazor aplikacja i aplikacja serwera współużytkują to samo środowisko.</span><span class="sxs-lookup"><span data-stu-id="39816-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="39816-118">Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="39816-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="39816-119">W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne.</span><span class="sxs-lookup"><span data-stu-id="39816-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="39816-120">Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="39816-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="39816-121">W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego pliku *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="39816-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="39816-122">Plik *Web. config* znajduje się w folderze *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="39816-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="39816-123">Aby użyć niestandardowego pliku *Web. config* dla usług IIS, które nie są zastępowane podczas publikowania aplikacji w folderze *publikowania* , zobacz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="39816-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="39816-124">Uzyskaj środowisko aplikacji w składniku, wprowadzając <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> i odczytując <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Właściwość:</span><span class="sxs-lookup"><span data-stu-id="39816-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="39816-125">Podczas uruchamiania program <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> ujawnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:</span><span class="sxs-lookup"><span data-stu-id="39816-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="39816-126">Następujące wygodne metody rozszerzające umożliwiają sprawdzanie bieżącego środowiska pod kątem nazw środowisk deweloperskich, produkcyjnych, tymczasowych i niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="39816-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="39816-127"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Właściwość może być używana podczas uruchamiania, gdy <xref:Microsoft.AspNetCore.Components.NavigationManager> Usługa jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="39816-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="39816-128">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="39816-128">Configuration</span></span>

Blazor<span data-ttu-id="39816-129">Zestaw webassembly ładuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="39816-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="39816-130">Pliki ustawień aplikacji domyślnie:</span><span class="sxs-lookup"><span data-stu-id="39816-130">App settings files by default:</span></span>
  * <span data-ttu-id="39816-131">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="39816-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="39816-132">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="39816-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="39816-133">Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="39816-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="39816-134">Nie wszyscy dostawcy są odpowiednim rozwiązaniem dla Blazor aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="39816-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="39816-135">Wyjaśnienie, na których dostawców są obsługiwane dla Blazor zestawu webassembly, są śledzone przez [wyjaśnienie dostawców konfiguracji dla Blazor WASM (dotnet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="39816-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="39816-136">Konfiguracja w Blazor aplikacji webassembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="39816-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="39816-137">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="39816-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="39816-138">Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="39816-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="39816-139">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="39816-139">App settings configuration</span></span>

<span data-ttu-id="39816-140">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="39816-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="39816-141">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="39816-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="39816-142">Konfiguracja dostawcy</span><span class="sxs-lookup"><span data-stu-id="39816-142">Provider configuration</span></span>

<span data-ttu-id="39816-143">Poniższy przykład używa, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> Aby podać dodatkową konfigurację:</span><span class="sxs-lookup"><span data-stu-id="39816-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="39816-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="39816-144">`Program.Main`:</span></span>

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

<span data-ttu-id="39816-145">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="39816-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="39816-146">Aby odczytać inne pliki konfiguracji z folderu *wwwroot* do konfiguracji, użyj programu <xref:System.Net.Http.HttpClient> w celu uzyskania zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="39816-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="39816-147">W przypadku korzystania z tego podejścia istniejąca <xref:System.Net.Http.HttpClient> rejestracja usługi może używać lokalnego klienta utworzonego do odczytu pliku, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="39816-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="39816-148">plik *wwwroot/samochody. JSON*:</span><span class="sxs-lookup"><span data-stu-id="39816-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="39816-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="39816-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="39816-150">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="39816-150">Authentication configuration</span></span>

<span data-ttu-id="39816-151">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="39816-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="39816-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="39816-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="39816-153">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="39816-153">Logging configuration</span></span>

<span data-ttu-id="39816-154">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="39816-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="39816-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="39816-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="39816-156">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="39816-156">Host builder configuration</span></span>

<span data-ttu-id="39816-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="39816-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="39816-158">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="39816-158">Cached configuration</span></span>

<span data-ttu-id="39816-159">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="39816-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="39816-160">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="39816-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="39816-161">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="39816-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="39816-162">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="39816-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="39816-163">Pliki *Service-Worker. js* i *Service-Worker-Assets. js* programu PWA muszą zostać ponownie skompilowane w ramach kompilacji, która sygnalizuje aplikacji w następnym trybie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="39816-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="39816-164">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="39816-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="39816-165">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="39816-165">Logging</span></span>

<span data-ttu-id="39816-166">Aby uzyskać informacje na temat Blazor obsługi rejestrowania zestawów webassembly, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="39816-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="39816-167">Server</span><span class="sxs-lookup"><span data-stu-id="39816-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="39816-168">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="39816-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="39816-169">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="39816-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="39816-170">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="39816-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="39816-171">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="39816-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="39816-172">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="39816-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="39816-173">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="39816-173">CSS class</span></span>                       | <span data-ttu-id="39816-174">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="39816-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="39816-175">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-176">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-177">'Blazor'</span></span>
- <span data-ttu-id="39816-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-178">'Identity'</span></span>
- <span data-ttu-id="39816-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-180">'Razor'</span></span>
- <span data-ttu-id="39816-181">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-182">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-183">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-184">'Blazor'</span></span>
- <span data-ttu-id="39816-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-185">'Identity'</span></span>
- <span data-ttu-id="39816-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-187">'Razor'</span></span>
- <span data-ttu-id="39816-188">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-189">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-190">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-191">'Blazor'</span></span>
- <span data-ttu-id="39816-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-192">'Identity'</span></span>
- <span data-ttu-id="39816-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-194">'Razor'</span></span>
- <span data-ttu-id="39816-195">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-196">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-197">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-198">'Blazor'</span></span>
- <span data-ttu-id="39816-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-199">'Identity'</span></span>
- <span data-ttu-id="39816-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-201">'Razor'</span></span>
- <span data-ttu-id="39816-202">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-203">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-204">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-205">'Blazor'</span></span>
- <span data-ttu-id="39816-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-206">'Identity'</span></span>
- <span data-ttu-id="39816-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-208">'Razor'</span></span>
- <span data-ttu-id="39816-209">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-210">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-211">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-212">'Blazor'</span></span>
- <span data-ttu-id="39816-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-213">'Identity'</span></span>
- <span data-ttu-id="39816-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-215">'Razor'</span></span>
- <span data-ttu-id="39816-216">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-217">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-218">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-219">'Blazor'</span></span>
- <span data-ttu-id="39816-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-220">'Identity'</span></span>
- <span data-ttu-id="39816-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-222">'Razor'</span></span>
- <span data-ttu-id="39816-223">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-224">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-225">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-226">'Blazor'</span></span>
- <span data-ttu-id="39816-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-227">'Identity'</span></span>
- <span data-ttu-id="39816-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-229">'Razor'</span></span>
- <span data-ttu-id="39816-230">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-231">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-232">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-233">'Blazor'</span></span>
- <span data-ttu-id="39816-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-234">'Identity'</span></span>
- <span data-ttu-id="39816-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-236">'Razor'</span></span>
- <span data-ttu-id="39816-237">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-238">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-239">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-240">'Blazor'</span></span>
- <span data-ttu-id="39816-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-241">'Identity'</span></span>
- <span data-ttu-id="39816-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-243">'Razor'</span></span>
- <span data-ttu-id="39816-244">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-245">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-246">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-247">'Blazor'</span></span>
- <span data-ttu-id="39816-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-248">'Identity'</span></span>
- <span data-ttu-id="39816-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-250">'Razor'</span></span>
- <span data-ttu-id="39816-251">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-252">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-253">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-254">'Blazor'</span></span>
- <span data-ttu-id="39816-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-255">'Identity'</span></span>
- <span data-ttu-id="39816-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-257">'Razor'</span></span>
- <span data-ttu-id="39816-258">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-259">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-260">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-261">'Blazor'</span></span>
- <span data-ttu-id="39816-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-262">'Identity'</span></span>
- <span data-ttu-id="39816-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-264">'Razor'</span></span>
- <span data-ttu-id="39816-265">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-265">'SignalR' uid:</span></span> 

<span data-ttu-id="39816-266">---------------- | ---title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-267">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-268">'Blazor'</span></span>
- <span data-ttu-id="39816-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-269">'Identity'</span></span>
- <span data-ttu-id="39816-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-271">'Razor'</span></span>
- <span data-ttu-id="39816-272">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-273">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-274">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-275">'Blazor'</span></span>
- <span data-ttu-id="39816-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-276">'Identity'</span></span>
- <span data-ttu-id="39816-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-278">'Razor'</span></span>
- <span data-ttu-id="39816-279">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-280">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-281">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-282">'Blazor'</span></span>
- <span data-ttu-id="39816-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-283">'Identity'</span></span>
- <span data-ttu-id="39816-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-285">'Razor'</span></span>
- <span data-ttu-id="39816-286">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-287">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-288">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-289">'Blazor'</span></span>
- <span data-ttu-id="39816-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-290">'Identity'</span></span>
- <span data-ttu-id="39816-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-292">'Razor'</span></span>
- <span data-ttu-id="39816-293">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-294">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-295">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-296">'Blazor'</span></span>
- <span data-ttu-id="39816-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-297">'Identity'</span></span>
- <span data-ttu-id="39816-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-299">'Razor'</span></span>
- <span data-ttu-id="39816-300">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39816-301">title: "ASP.NET Core Blazor Konfiguracja modelu hostingu" autor: Opis: "informacje o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC".</span><span class="sxs-lookup"><span data-stu-id="39816-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="39816-302">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39816-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39816-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39816-303">'Blazor'</span></span>
- <span data-ttu-id="39816-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39816-304">'Identity'</span></span>
- <span data-ttu-id="39816-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39816-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="39816-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39816-306">'Razor'</span></span>
- <span data-ttu-id="39816-307">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39816-307">'SignalR' uid:</span></span> 

<span data-ttu-id="39816-308">--------- | | `components-reconnect-show`     | Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="39816-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="39816-309">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="39816-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="39816-310">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="39816-310">Show the modal.</span></span> <span data-ttu-id="39816-311">| | `components-reconnect-hide`     | Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="39816-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="39816-312">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="39816-312">Hide the modal.</span></span> <span data-ttu-id="39816-313">| | `components-reconnect-failed`   | Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="39816-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="39816-314">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="39816-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="39816-315">| | `components-reconnect-rejected` | Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="39816-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="39816-316">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="39816-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="39816-317">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="39816-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="39816-318">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="39816-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="39816-319">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="39816-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="39816-320">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="39816-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="39816-321">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="39816-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="39816-322">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="39816-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="39816-323">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="39816-323">Render mode</span></span>

Blazor<span data-ttu-id="39816-324">Aplikacje serwera są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="39816-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="39816-325">Ta konfiguracja jest ustawiana na stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="39816-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="39816-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="39816-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="39816-327">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="39816-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="39816-328">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="39816-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="39816-329">Opis</span><span class="sxs-lookup"><span data-stu-id="39816-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="39816-330">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="39816-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="39816-331">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="39816-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="39816-332">Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="39816-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="39816-333">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="39816-333">Output from the component isn't included.</span></span> <span data-ttu-id="39816-334">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="39816-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="39816-335">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="39816-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="39816-336">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="39816-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="39816-337">Konfigurowanie SignalR klienta dla Blazor aplikacji serwerowych</span><span class="sxs-lookup"><span data-stu-id="39816-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="39816-338">Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="39816-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="39816-339">Na przykład możesz chcieć skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="39816-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="39816-340">Aby skonfigurować SignalR klienta w pliku *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="39816-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="39816-341">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="39816-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="39816-342">Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="39816-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="39816-343">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="39816-343">Logging</span></span>

<span data-ttu-id="39816-344">Aby uzyskać informacje na temat Blazor obsługi rejestrowania na serwerze, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="39816-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
