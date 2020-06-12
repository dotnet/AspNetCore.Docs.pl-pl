---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Dowiedz się więcej o Blazor konfiguracji modelu hostingu, w tym o sposobie integrowania Razor składników ze Razor stronami i aplikacjami MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 3cef67806ce0e2e045122bdc962e93795be68572
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679582"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="bd4da-103">ASP.NET Core Blazor konfigurację modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="bd4da-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="bd4da-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd4da-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd4da-105">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="bd4da-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="bd4da-106">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="bd4da-106"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="bd4da-107">Środowisko</span><span class="sxs-lookup"><span data-stu-id="bd4da-107">Environment</span></span>

<span data-ttu-id="bd4da-108">Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane.</span><span class="sxs-lookup"><span data-stu-id="bd4da-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="bd4da-109">Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="bd4da-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="bd4da-110">Hostowana Blazor aplikacja sieci webassembly pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="bd4da-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="bd4da-111">Wartość nagłówka to środowisko.</span><span class="sxs-lookup"><span data-stu-id="bd4da-111">The value of the header is the environment.</span></span> <span data-ttu-id="bd4da-112">Hostowana Blazor aplikacja i aplikacja serwera współużytkują to samo środowisko.</span><span class="sxs-lookup"><span data-stu-id="bd4da-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="bd4da-113">Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="bd4da-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bd4da-114">W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne.</span><span class="sxs-lookup"><span data-stu-id="bd4da-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="bd4da-115">Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="bd4da-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="bd4da-116">W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="bd4da-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="bd4da-117">Plik *web.config* znajduje się w folderze *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="bd4da-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="bd4da-118">Aby użyć niestandardowego pliku *web.config* dla usług IIS, które nie są zastępowane podczas publikowania aplikacji w folderze *publikowania* , zobacz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="bd4da-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="bd4da-119">Uzyskaj środowisko aplikacji w składniku, wprowadzając <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> i odczytując <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Właściwość:</span><span class="sxs-lookup"><span data-stu-id="bd4da-119">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="bd4da-120">Podczas uruchamiania program <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> ujawnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:</span><span class="sxs-lookup"><span data-stu-id="bd4da-120">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="bd4da-121">Następujące wygodne metody rozszerzające umożliwiają sprawdzanie bieżącego środowiska pod kątem nazw środowisk deweloperskich, produkcyjnych, tymczasowych i niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="bd4da-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="bd4da-122"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Właściwość może być używana podczas uruchamiania, gdy <xref:Microsoft.AspNetCore.Components.NavigationManager> Usługa jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="bd4da-122">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="bd4da-123">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="bd4da-123">Configuration</span></span>

Blazor<span data-ttu-id="bd4da-124">Zestaw webassembly ładuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="bd4da-124"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="bd4da-125">Pliki ustawień aplikacji domyślnie:</span><span class="sxs-lookup"><span data-stu-id="bd4da-125">App settings files by default:</span></span>
  * <span data-ttu-id="bd4da-126">*plik wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="bd4da-126">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="bd4da-127">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="bd4da-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="bd4da-128">Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="bd4da-128">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="bd4da-129">Nie wszyscy dostawcy są odpowiednim rozwiązaniem dla Blazor aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="bd4da-129">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="bd4da-130">Wyjaśnienie, na których dostawców są obsługiwane dla Blazor zestawu webassembly, są śledzone przez [wyjaśnienie dostawców konfiguracji dla Blazor WASM (#18134 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="bd4da-130">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="bd4da-131">Konfiguracja w Blazor aplikacji webassembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bd4da-131">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="bd4da-132">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="bd4da-132">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="bd4da-133">Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="bd4da-133">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="bd4da-134">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="bd4da-134">App settings configuration</span></span>

<span data-ttu-id="bd4da-135">plik *wwwroot/appsettings.jsw*:</span><span class="sxs-lookup"><span data-stu-id="bd4da-135">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="bd4da-136">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="bd4da-136">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="bd4da-137">Konfiguracja dostawcy</span><span class="sxs-lookup"><span data-stu-id="bd4da-137">Provider configuration</span></span>

<span data-ttu-id="bd4da-138">Poniższy przykład używa, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> Aby podać dodatkową konfigurację:</span><span class="sxs-lookup"><span data-stu-id="bd4da-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="bd4da-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bd4da-139">`Program.Main`:</span></span>

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

<span data-ttu-id="bd4da-140">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="bd4da-140">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="bd4da-141">Aby odczytać inne pliki konfiguracji z folderu *wwwroot* do konfiguracji, użyj programu <xref:System.Net.Http.HttpClient> w celu uzyskania zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="bd4da-141">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="bd4da-142">W przypadku korzystania z tego podejścia istniejąca <xref:System.Net.Http.HttpClient> rejestracja usługi może używać lokalnego klienta utworzonego do odczytu pliku, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="bd4da-142">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="bd4da-143">plik *wwwroot/cars.jsw*:</span><span class="sxs-lookup"><span data-stu-id="bd4da-143">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="bd4da-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bd4da-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="bd4da-145">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bd4da-145">Authentication configuration</span></span>

<span data-ttu-id="bd4da-146">plik *wwwroot/appsettings.jsw*:</span><span class="sxs-lookup"><span data-stu-id="bd4da-146">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="bd4da-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bd4da-147">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="bd4da-148">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="bd4da-148">Logging configuration</span></span>

<span data-ttu-id="bd4da-149">Dodaj odwołanie do pakietu dla [Microsoft.Extensions.Logging.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="bd4da-149">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="bd4da-150">plik *wwwroot/appsettings.jsw*:</span><span class="sxs-lookup"><span data-stu-id="bd4da-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="bd4da-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bd4da-151">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="bd4da-152">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="bd4da-152">Host builder configuration</span></span>

<span data-ttu-id="bd4da-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="bd4da-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="bd4da-154">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="bd4da-154">Cached configuration</span></span>

<span data-ttu-id="bd4da-155">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="bd4da-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="bd4da-156">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="bd4da-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="bd4da-157">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="bd4da-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="bd4da-158">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="bd4da-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="bd4da-159">Pliki *service-worker.js* i *service-worker-assets.js* programu PWA muszą zostać ponownie skompilowane w ramach kompilacji, która sygnalizuje aplikacji w następnym trybie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="bd4da-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="bd4da-160">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="bd4da-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="bd4da-161">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="bd4da-161">Logging</span></span>

<span data-ttu-id="bd4da-162">Aby uzyskać informacje na temat Blazor obsługi rejestrowania zestawów webassembly, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="bd4da-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="bd4da-163">negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bd4da-163"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="bd4da-164">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak pliki cookie lub nagłówki uwierzytelniania http:</span><span class="sxs-lookup"><span data-stu-id="bd4da-164">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="bd4da-165">Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> dla żądań [pobrania](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) między źródłami:</span><span class="sxs-lookup"><span data-stu-id="bd4da-165">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="bd4da-166">Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="bd4da-166">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var client = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="bd4da-167">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="bd4da-167">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="bd4da-168">Server</span><span class="sxs-lookup"><span data-stu-id="bd4da-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="bd4da-169">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="bd4da-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="bd4da-170">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="bd4da-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="bd4da-171">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="bd4da-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="bd4da-172">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bd4da-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="bd4da-173">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="bd4da-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="bd4da-174">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="bd4da-174">CSS class</span></span>                       | <span data-ttu-id="bd4da-175">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="bd4da-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="bd4da-176">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="bd4da-176">A lost connection.</span></span> <span data-ttu-id="bd4da-177">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="bd4da-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="bd4da-178">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="bd4da-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="bd4da-179">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="bd4da-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="bd4da-180">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="bd4da-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="bd4da-181">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="bd4da-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="bd4da-182">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="bd4da-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="bd4da-183">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="bd4da-183">Reconnection rejected.</span></span> <span data-ttu-id="bd4da-184">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="bd4da-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="bd4da-185">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="bd4da-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="bd4da-186">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="bd4da-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="bd4da-187">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="bd4da-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="bd4da-188">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bd4da-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="bd4da-189">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="bd4da-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="bd4da-190">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="bd4da-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="bd4da-191">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="bd4da-191">Render mode</span></span>

Blazor<span data-ttu-id="bd4da-192">Aplikacje serwera są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="bd4da-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="bd4da-193">Ta konfiguracja jest ustawiana na stronie *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="bd4da-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="bd4da-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="bd4da-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="bd4da-195">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="bd4da-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="bd4da-196">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bd4da-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="bd4da-197">Opis</span><span class="sxs-lookup"><span data-stu-id="bd4da-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="bd4da-198">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="bd4da-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bd4da-199">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bd4da-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="bd4da-200">Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="bd4da-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bd4da-201">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="bd4da-201">Output from the component isn't included.</span></span> <span data-ttu-id="bd4da-202">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bd4da-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="bd4da-203">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="bd4da-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="bd4da-204">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="bd4da-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="bd4da-205">Konfigurowanie SignalR klienta dla Blazor aplikacji serwerowych</span><span class="sxs-lookup"><span data-stu-id="bd4da-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="bd4da-206">Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="bd4da-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="bd4da-207">Na przykład możesz chcieć skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="bd4da-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="bd4da-208">Aby skonfigurować SignalR klienta w pliku *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bd4da-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="bd4da-209">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="bd4da-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="bd4da-210">Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="bd4da-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="bd4da-211">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="bd4da-211">Logging</span></span>

<span data-ttu-id="bd4da-212">Aby uzyskać informacje na temat Blazor obsługi rejestrowania na serwerze, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="bd4da-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
