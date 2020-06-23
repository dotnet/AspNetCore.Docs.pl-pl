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
ms.openlocfilehash: 0e36b81d771b07e85158724c02210ee50a3ab118
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242683"
---
# <a name="aspnet-core-blazor-configuration"></a>BlazorKonfiguracja ASP.NET Core

> [!NOTE]
> Ten temat dotyczy Blazor zestawu webassembly. Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core można znaleźć w temacie <xref:fundamentals/configuration/index> .

BlazorZestaw webassembly ładuje konfigurację z:

* Pliki ustawień aplikacji domyślnie:
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację. Nie wszyscy dostawcy są odpowiednim rozwiązaniem dla Blazor aplikacji webassembly. Wyjaśnienie, na których dostawców są obsługiwane dla Blazor zestawu webassembly, są śledzone przez [wyjaśnienie dostawców konfiguracji dla Blazor WASM (#18134 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> Konfiguracja w Blazor aplikacji webassembly jest widoczna dla użytkowników. **Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**

Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Konfiguracja ustawień aplikacji

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a>Konfiguracja dostawcy

Poniższy przykład używa, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> Aby podać dodatkową konfigurację:

`Program.Main`:

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

Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:

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

Aby odczytać inne pliki konfiguracji z `wwwroot` folderu do konfiguracji, użyj programu <xref:System.Net.Http.HttpClient> w celu uzyskania zawartości pliku. W przypadku korzystania z tego podejścia istniejąca <xref:System.Net.Http.HttpClient> rejestracja usługi może używać lokalnego klienta utworzonego do odczytu pliku, jak pokazano w poniższym przykładzie:

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

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

## <a name="authentication-configuration"></a>Konfiguracja uwierzytelniania

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Konfiguracja rejestrowania

Dodaj odwołanie do pakietu dla [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

`wwwroot/appsettings.json`:

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Konfiguracja konstruktora hostów

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Konfiguracja buforowana

Pliki konfiguracji są buforowane do użycia w trybie offline. Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia. Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:

* Użytkownicy mają buforowane wersje plików, które nadal są używane.
* W przypadku `service-worker.js` kompilacji i `service-worker-assets.js` plików programu PWA należy ponownie skompilować, który sygnalizuje aplikacji na następnej stronie online, że aplikacja została ponownie wdrożona.

Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .
