---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Dowiedz Blazor się więcej o konfiguracji modelu hostingu, w Razor Razor tym o sposobie integrowania składników ze stronami i aplikacjami MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772077"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="86803-103">Konfiguracja modelu hostingu ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="86803-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="86803-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="86803-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="86803-105">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="86803-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="86803-106">Zestaw WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="86803-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="86803-107">Środowisko</span><span class="sxs-lookup"><span data-stu-id="86803-107">Environment</span></span>

<span data-ttu-id="86803-108">Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane.</span><span class="sxs-lookup"><span data-stu-id="86803-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="86803-109">Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="86803-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="86803-110">Hostowana aplikacja webassembly Blazor pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="86803-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="86803-111">Wartość nagłówka to środowisko.</span><span class="sxs-lookup"><span data-stu-id="86803-111">The value of the header is the environment.</span></span> <span data-ttu-id="86803-112">Hostowana aplikacja Blazor i aplikacja serwera współużytkują to samo środowisko.</span><span class="sxs-lookup"><span data-stu-id="86803-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="86803-113">Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, <xref:fundamentals/environments>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="86803-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="86803-114">W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne.</span><span class="sxs-lookup"><span data-stu-id="86803-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="86803-115">Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="86803-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="86803-116">W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego pliku *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="86803-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="86803-117">Plik *Web. config* znajduje się w folderze *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="86803-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="86803-118">Aby użyć niestandardowego pliku *Web. config* dla usług IIS, które nie są zastępowane podczas publikowania aplikacji w folderze *publikowania* , zobacz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="86803-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="86803-119">Uzyskaj środowisko aplikacji w składniku, wprowadzając `IWebAssemblyHostEnvironment` i odczytując `Environment` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="86803-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="86803-120">Podczas uruchamiania program `WebAssemblyHostBuilder` ujawnia `IWebAssemblyHostEnvironment` `HostEnvironment` Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:</span><span class="sxs-lookup"><span data-stu-id="86803-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="86803-121">Następujące wygodne metody rozszerzające umożliwiają sprawdzanie bieżącego środowiska pod kątem nazw środowisk deweloperskich, produkcyjnych, tymczasowych i niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="86803-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="86803-122">"Isenvironment (" {Nazwa środowiska} ")</span><span class="sxs-lookup"><span data-stu-id="86803-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="86803-123">`IWebAssemblyHostEnvironment.BaseAddress` Właściwość może być używana podczas uruchamiania, `NavigationManager` gdy usługa jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="86803-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="86803-124">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="86803-124">Configuration</span></span>

<span data-ttu-id="86803-125">Blazor webassembly ładuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="86803-125">Blazor WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="86803-126">Pliki ustawień aplikacji domyślnie:</span><span class="sxs-lookup"><span data-stu-id="86803-126">App settings files by default:</span></span>
  * <span data-ttu-id="86803-127">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="86803-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="86803-128">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="86803-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="86803-129">Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="86803-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="86803-130">Nie wszyscy dostawcy są odpowiednią do aplikacji Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="86803-130">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="86803-131">Wyjaśnienie, na których dostawców są obsługiwane Blazor webassembly, jest śledzone przez [wyjaśnienie dostawców konfiguracji dla BLAZOR WASM (dotnet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="86803-131">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="86803-132">Konfiguracja w aplikacji Blazor webassembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="86803-132">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="86803-133">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="86803-133">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="86803-134">Aby uzyskać więcej informacji na temat dostawców konfiguracji <xref:fundamentals/configuration/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="86803-134">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="86803-135">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="86803-135">App settings configuration</span></span>

<span data-ttu-id="86803-136">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="86803-136">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="86803-137">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="86803-137">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="86803-138">Konfiguracja dostawcy</span><span class="sxs-lookup"><span data-stu-id="86803-138">Provider configuration</span></span>

<span data-ttu-id="86803-139">Poniższy przykład używa, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> aby podać dodatkową konfigurację:</span><span class="sxs-lookup"><span data-stu-id="86803-139">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="86803-140">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86803-140">`Program.Main`:</span></span>

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

<span data-ttu-id="86803-141">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="86803-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="86803-142">Aby odczytać inne pliki konfiguracji z folderu *wwwroot* do konfiguracji, użyj programu `HttpClient` w celu uzyskania zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="86803-142">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="86803-143">W przypadku korzystania z tego podejścia istniejąca `HttpClient` Rejestracja usługi może używać lokalnego klienta utworzonego do odczytu pliku, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="86803-143">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="86803-144">plik *wwwroot/samochody. JSON*:</span><span class="sxs-lookup"><span data-stu-id="86803-144">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="86803-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86803-145">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="86803-146">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="86803-146">Authentication configuration</span></span>

<span data-ttu-id="86803-147">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="86803-147">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="86803-148">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86803-148">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="86803-149">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="86803-149">Logging configuration</span></span>

<span data-ttu-id="86803-150">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="86803-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="86803-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86803-151">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="86803-152">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="86803-152">Host builder configuration</span></span>

<span data-ttu-id="86803-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="86803-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="86803-154">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="86803-154">Cached configuration</span></span>

<span data-ttu-id="86803-155">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="86803-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="86803-156">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="86803-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="86803-157">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="86803-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="86803-158">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="86803-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="86803-159">Pliki *Service-Worker. js* i *Service-Worker-Assets. js* programu PWA muszą zostać ponownie skompilowane w ramach kompilacji, która sygnalizuje aplikacji w następnym trybie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="86803-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="86803-160">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane <xref:blazor/progressive-web-app#background-updates>przez PWAs, zobacz.</span><span class="sxs-lookup"><span data-stu-id="86803-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="86803-161">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="86803-161">Logging</span></span>

<span data-ttu-id="86803-162">Aby uzyskać informacje na temat obsługi rejestrowania w programie Blazor <xref:fundamentals/logging/index#create-logs-in-blazor>webassembly, zobacz.</span><span class="sxs-lookup"><span data-stu-id="86803-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="86803-163">Serwer Blazor</span><span class="sxs-lookup"><span data-stu-id="86803-163">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="86803-164">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="86803-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="86803-165">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="86803-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="86803-166">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="86803-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="86803-167">Aby dostosować interfejs użytkownika, zdefiniuj element `id` z elementu `components-reconnect-modal` na liście `<body>` *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="86803-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="86803-168">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="86803-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="86803-169">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="86803-169">CSS class</span></span>                       | <span data-ttu-id="86803-170">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="86803-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="86803-171">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="86803-171">A lost connection.</span></span> <span data-ttu-id="86803-172">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="86803-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="86803-173">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="86803-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="86803-174">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="86803-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="86803-175">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="86803-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="86803-176">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="86803-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="86803-177">Aby spróbować ponownie nawiązać połączenie `window.Blazor.reconnect()`, wywołaj polecenie.</span><span class="sxs-lookup"><span data-stu-id="86803-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="86803-178">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="86803-178">Reconnection rejected.</span></span> <span data-ttu-id="86803-179">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="86803-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="86803-180">Aby ponownie załadować aplikację, wywołaj `location.reload()`polecenie.</span><span class="sxs-lookup"><span data-stu-id="86803-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="86803-181">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="86803-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="86803-182">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="86803-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="86803-183">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="86803-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="86803-184">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="86803-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="86803-185">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="86803-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="86803-186">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="86803-186">Render mode</span></span>

<span data-ttu-id="86803-187">Aplikacje serwera Blazor są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="86803-187">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="86803-188">Ta konfiguracja jest ustawiana na stronie *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="86803-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="86803-189">`RenderMode`Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="86803-189">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="86803-190">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="86803-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="86803-191">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia aplikacji Blazor z poziomu agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="86803-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="86803-192">Opis</span><span class="sxs-lookup"><span data-stu-id="86803-192">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="86803-193">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="86803-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="86803-194">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="86803-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="86803-195">Renderuje znacznik dla aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="86803-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="86803-196">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="86803-196">Output from the component isn't included.</span></span> <span data-ttu-id="86803-197">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="86803-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="86803-198">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="86803-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="86803-199">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="86803-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="86803-200">Konfigurowanie SignalR klienta dla Blazor aplikacji serwerowych</span><span class="sxs-lookup"><span data-stu-id="86803-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="86803-201">Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="86803-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="86803-202">Na przykład możesz chcieć skonfigurować rejestrowanie na kliencie, SignalR aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="86803-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="86803-203">Aby skonfigurować SignalR klienta w pliku *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="86803-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="86803-204">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="86803-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="86803-205">Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="86803-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="86803-206">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="86803-206">Logging</span></span>

<span data-ttu-id="86803-207">Aby uzyskać informacje Blazor na temat obsługi rejestrowania na <xref:fundamentals/logging/index#create-logs-in-blazor>serwerze, zobacz.</span><span class="sxs-lookup"><span data-stu-id="86803-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
