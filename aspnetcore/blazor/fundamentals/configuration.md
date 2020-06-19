---
title: BlazorKonfiguracja ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat konfiguracji Blazor aplikacji, w tym ustawień aplikacji, uwierzytelniania i konfiguracji rejestrowania.
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103851"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="63d6c-103">BlazorKonfiguracja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63d6c-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="63d6c-104">Ten temat dotyczy Blazor zestawu webassembly.</span><span class="sxs-lookup"><span data-stu-id="63d6c-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="63d6c-105">Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core można znaleźć w temacie <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="63d6c-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="63d6c-106">Zestaw webassembly ładuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="63d6c-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="63d6c-107">Pliki ustawień aplikacji domyślnie:</span><span class="sxs-lookup"><span data-stu-id="63d6c-107">App settings files by default:</span></span>
  * <span data-ttu-id="63d6c-108">*plik wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="63d6c-108">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="63d6c-109">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="63d6c-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="63d6c-110">Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="63d6c-110">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="63d6c-111">Nie wszyscy dostawcy są odpowiednim rozwiązaniem dla Blazor aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="63d6c-111">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="63d6c-112">Wyjaśnienie, na których dostawców są obsługiwane dla Blazor zestawu webassembly, są śledzone przez [wyjaśnienie dostawców konfiguracji dla Blazor WASM (#18134 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="63d6c-112">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="63d6c-113">Konfiguracja w Blazor aplikacji webassembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="63d6c-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="63d6c-114">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="63d6c-114">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="63d6c-115">Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="63d6c-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="63d6c-116">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="63d6c-116">App settings configuration</span></span>

<span data-ttu-id="63d6c-117">plik *wwwroot/appsettings.jsw*:</span><span class="sxs-lookup"><span data-stu-id="63d6c-117">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="63d6c-118">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="63d6c-118">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="63d6c-119">Konfiguracja dostawcy</span><span class="sxs-lookup"><span data-stu-id="63d6c-119">Provider configuration</span></span>

<span data-ttu-id="63d6c-120">Poniższy przykład używa, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> Aby podać dodatkową konfigurację:</span><span class="sxs-lookup"><span data-stu-id="63d6c-120">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="63d6c-121">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="63d6c-121">`Program.Main`:</span></span>

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

<span data-ttu-id="63d6c-122">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="63d6c-122">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="63d6c-123">Aby odczytać inne pliki konfiguracji z folderu *wwwroot* do konfiguracji, użyj programu <xref:System.Net.Http.HttpClient> w celu uzyskania zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="63d6c-123">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="63d6c-124">W przypadku korzystania z tego podejścia istniejąca <xref:System.Net.Http.HttpClient> rejestracja usługi może używać lokalnego klienta utworzonego do odczytu pliku, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="63d6c-124">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="63d6c-125">plik *wwwroot/cars.jsw*:</span><span class="sxs-lookup"><span data-stu-id="63d6c-125">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="63d6c-126">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="63d6c-126">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="63d6c-127">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="63d6c-127">Authentication configuration</span></span>

<span data-ttu-id="63d6c-128">plik *wwwroot/appsettings.jsw*:</span><span class="sxs-lookup"><span data-stu-id="63d6c-128">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="63d6c-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="63d6c-129">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="63d6c-130">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="63d6c-130">Logging configuration</span></span>

<span data-ttu-id="63d6c-131">Dodaj odwołanie do pakietu dla [Microsoft.Extensions.Logging.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="63d6c-131">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="63d6c-132">plik *wwwroot/appsettings.jsw*:</span><span class="sxs-lookup"><span data-stu-id="63d6c-132">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="63d6c-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="63d6c-133">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="63d6c-134">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="63d6c-134">Host builder configuration</span></span>

<span data-ttu-id="63d6c-135">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="63d6c-135">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="63d6c-136">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="63d6c-136">Cached configuration</span></span>

<span data-ttu-id="63d6c-137">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="63d6c-137">Configuration files are cached for offline use.</span></span> <span data-ttu-id="63d6c-138">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="63d6c-138">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="63d6c-139">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="63d6c-139">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="63d6c-140">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="63d6c-140">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="63d6c-141">Pliki *service-worker.js* i *service-worker-assets.js* programu PWA muszą zostać ponownie skompilowane w ramach kompilacji, która sygnalizuje aplikacji w następnym trybie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="63d6c-141">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="63d6c-142">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="63d6c-142">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
