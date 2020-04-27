---
title: ASP.NET Core Blazor konfigurację modelu hostingu
author: guardrex
description: Dowiedz Blazor się więcej o konfiguracji modelu hostingu, w tym o sposobie integrowania składników Razor z aplikacjami Razor Pages i MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: c7e8d1f2dcba6432072a5cc11a6c5d78e50c2398
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159622"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="e432c-103">Konfiguracja modelu hostingu ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e432c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="e432c-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e432c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e432c-105">W tym artykule opisano hostowanie konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="e432c-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="e432c-106">Zestaw WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="e432c-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="e432c-107">Środowisko</span><span class="sxs-lookup"><span data-stu-id="e432c-107">Environment</span></span>

<span data-ttu-id="e432c-108">Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane.</span><span class="sxs-lookup"><span data-stu-id="e432c-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="e432c-109">Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="e432c-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="e432c-110">Hostowana aplikacja webassembly Blazor pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="e432c-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="e432c-111">Wartość nagłówka to środowisko.</span><span class="sxs-lookup"><span data-stu-id="e432c-111">The value of the header is the environment.</span></span> <span data-ttu-id="e432c-112">Hostowana aplikacja Blazor i aplikacja serwera współużytkują to samo środowisko.</span><span class="sxs-lookup"><span data-stu-id="e432c-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="e432c-113">Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, <xref:fundamentals/environments>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="e432c-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e432c-114">W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne.</span><span class="sxs-lookup"><span data-stu-id="e432c-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="e432c-115">Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="e432c-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="e432c-116">W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego pliku *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="e432c-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="e432c-117">Plik *Web. config* znajduje się w folderze *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="e432c-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="e432c-118">Aby użyć niestandardowego pliku *Web. config* dla usług IIS, które nie są zastępowane podczas publikowania aplikacji w folderze *publikowania* , zobacz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e432c-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="e432c-119">Uzyskaj środowisko aplikacji w składniku, wprowadzając `IWebAssemblyHostEnvironment` i odczytując `Environment` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="e432c-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="e432c-120">Podczas uruchamiania program `WebAssemblyHostBuilder` ujawnia `IWebAssemblyHostEnvironment` `HostEnvironment` Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:</span><span class="sxs-lookup"><span data-stu-id="e432c-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="e432c-121">Następujące wygodne metody rozszerzające umożliwiają sprawdzanie bieżącego środowiska pod kątem nazw środowisk deweloperskich, produkcyjnych, tymczasowych i niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="e432c-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="e432c-122">"Isenvironment (" {Nazwa środowiska} ")</span><span class="sxs-lookup"><span data-stu-id="e432c-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="e432c-123">`IWebAssemblyHostEnvironment.BaseAddress` Właściwość może być używana podczas uruchamiania, `NavigationManager` gdy usługa jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="e432c-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="e432c-124">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="e432c-124">Configuration</span></span>

<span data-ttu-id="e432c-125">Zestaw webassembly Blazor obsługuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="e432c-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="e432c-126">Domyślnie [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) dla plików ustawień aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e432c-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="e432c-127">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="e432c-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="e432c-128">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="e432c-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="e432c-129">Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="e432c-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="e432c-130">Konfiguracja w aplikacji Blazor webassembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="e432c-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="e432c-131">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="e432c-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="e432c-132">Aby uzyskać więcej informacji na temat dostawców konfiguracji <xref:fundamentals/configuration/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="e432c-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="e432c-133">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="e432c-133">App settings configuration</span></span>

<span data-ttu-id="e432c-134">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e432c-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="e432c-135">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="e432c-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="e432c-136">Konfiguracja dostawcy</span><span class="sxs-lookup"><span data-stu-id="e432c-136">Provider configuration</span></span>

<span data-ttu-id="e432c-137">Poniższy przykład używa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> [dostawcy konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) i dostarcza dodatkową konfigurację:</span><span class="sxs-lookup"><span data-stu-id="e432c-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="e432c-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e432c-138">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

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

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

<span data-ttu-id="e432c-139">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="e432c-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a><span data-ttu-id="e432c-140">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="e432c-140">Authentication configuration</span></span>

<span data-ttu-id="e432c-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e432c-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="e432c-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e432c-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="e432c-143">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="e432c-143">Logging configuration</span></span>

<span data-ttu-id="e432c-144">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e432c-144">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="e432c-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e432c-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="e432c-146">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="e432c-146">Host builder configuration</span></span>

<span data-ttu-id="e432c-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e432c-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="e432c-148">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="e432c-148">Cached configuration</span></span>

<span data-ttu-id="e432c-149">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="e432c-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="e432c-150">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="e432c-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="e432c-151">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="e432c-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="e432c-152">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="e432c-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="e432c-153">Pliki *Service-Worker. js* i *Service-Worker-Assets. js* programu PWA muszą zostać ponownie skompilowane w ramach kompilacji, która sygnalizuje aplikacji w następnym trybie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="e432c-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="e432c-154">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane <xref:blazor/progressive-web-app#background-updates>przez PWAs, zobacz.</span><span class="sxs-lookup"><span data-stu-id="e432c-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="e432c-155">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="e432c-155">Logging</span></span>

<span data-ttu-id="e432c-156">Aby uzyskać informacje na temat obsługi rejestrowania w programie Blazor <xref:fundamentals/logging/index#create-logs-in-blazor>webassembly, zobacz.</span><span class="sxs-lookup"><span data-stu-id="e432c-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="e432c-157">Serwer Blazor</span><span class="sxs-lookup"><span data-stu-id="e432c-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="e432c-158">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="e432c-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="e432c-159">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="e432c-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="e432c-160">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="e432c-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="e432c-161">Aby dostosować interfejs użytkownika, zdefiniuj element `id` z elementu `components-reconnect-modal` na liście `<body>` *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="e432c-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="e432c-162">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="e432c-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="e432c-163">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="e432c-163">CSS class</span></span>                       | <span data-ttu-id="e432c-164">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="e432c-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="e432c-165">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="e432c-165">A lost connection.</span></span> <span data-ttu-id="e432c-166">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="e432c-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="e432c-167">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="e432c-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="e432c-168">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="e432c-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="e432c-169">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="e432c-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="e432c-170">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="e432c-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="e432c-171">Aby spróbować ponownie nawiązać połączenie `window.Blazor.reconnect()`, wywołaj polecenie.</span><span class="sxs-lookup"><span data-stu-id="e432c-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="e432c-172">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="e432c-172">Reconnection rejected.</span></span> <span data-ttu-id="e432c-173">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="e432c-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="e432c-174">Aby ponownie załadować aplikację, wywołaj `location.reload()`polecenie.</span><span class="sxs-lookup"><span data-stu-id="e432c-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="e432c-175">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="e432c-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="e432c-176">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="e432c-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="e432c-177">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e432c-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="e432c-178">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="e432c-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="e432c-179">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="e432c-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="e432c-180">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="e432c-180">Render mode</span></span>

<span data-ttu-id="e432c-181">Aplikacje serwera Blazor są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="e432c-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="e432c-182">Ta konfiguracja jest ustawiana na stronie *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="e432c-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="e432c-183">`RenderMode`Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="e432c-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="e432c-184">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="e432c-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="e432c-185">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia aplikacji Blazor z poziomu agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e432c-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="e432c-186">Opis</span><span class="sxs-lookup"><span data-stu-id="e432c-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="e432c-187">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="e432c-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e432c-188">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e432c-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="e432c-189">Renderuje znacznik dla aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="e432c-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e432c-190">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="e432c-190">Output from the component isn't included.</span></span> <span data-ttu-id="e432c-191">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e432c-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="e432c-192">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="e432c-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="e432c-193">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="e432c-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="e432c-194">Konfigurowanie SignalR klienta dla Blazor aplikacji serwerowych</span><span class="sxs-lookup"><span data-stu-id="e432c-194">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="e432c-195">Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="e432c-195">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="e432c-196">Na przykład możesz chcieć skonfigurować rejestrowanie na kliencie, SignalR aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="e432c-196">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="e432c-197">Aby skonfigurować SignalR klienta w pliku *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="e432c-197">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="e432c-198">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="e432c-198">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e432c-199">Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="e432c-199">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="e432c-200">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="e432c-200">Logging</span></span>

<span data-ttu-id="e432c-201">Aby uzyskać informacje Blazor na temat obsługi rejestrowania na <xref:fundamentals/logging/index#create-logs-in-blazor>serwerze, zobacz.</span><span class="sxs-lookup"><span data-stu-id="e432c-201">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
