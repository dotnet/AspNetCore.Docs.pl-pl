---
title: BlazorKonfiguracja ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat konfiguracji Blazor aplikacji, w tym ustawień aplikacji, uwierzytelniania i konfiguracji rejestrowania.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485970"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>BlazorKonfiguracja ASP.NET Core

> [!NOTE]
> Ten temat ma zastosowanie do programu Blazor WebAssembly . Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core można znaleźć w temacie <xref:fundamentals/configuration/index> .

Blazor WebAssembly ładuje domyślnie konfigurację z następujących plików ustawień aplikacji:

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`, gdzie `{ENVIRONMENT}` symbol zastępczy to [środowisko środowiska uruchomieniowego](xref:fundamentals/environments)aplikacji.

Inni dostawcy konfiguracji zarejestrowani przez aplikację mogą także zapewnić konfigurację, ale nie wszyscy dostawcy lub funkcje dostawcy są odpowiednie dla Blazor WebAssembly aplikacji:

* [Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration): Dostawca nie jest obsługiwany dla tożsamości zarządzanej i identyfikatora aplikacji (identyfikator klienta) w scenariuszach tajnych klienta. Identyfikator aplikacji ze tajemnicą klienta nie jest zalecany dla żadnej aplikacji ASP.NET Core, w szczególności Blazor WebAssembly aplikacji, ponieważ klucz tajny klienta nie może być zabezpieczony po stronie klienta w celu uzyskania dostępu do usługi Azure Key Vault.
* [Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): Dostawca nie jest odpowiedni dla Blazor WebAssembly aplikacji, ponieważ Blazor WebAssembly aplikacje nie są uruchamiane na serwerze na platformie Azure.

> [!WARNING]
> Konfiguracja w Blazor WebAssembly aplikacji jest widoczna dla użytkowników. **Nie przechowuj wpisów tajnych aplikacji, poświadczeń ani innych poufnych danych w konfiguracji Blazor WebAssembly aplikacji.**

Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Konfiguracja ustawień aplikacji

Konfiguracja w plikach ustawień aplikacji jest domyślnie ładowana. W poniższym przykładzie wartość konfiguracji interfejsu użytkownika jest przechowywana w pliku ustawień aplikacji i automatycznie ładowana przez Blazor platformę. Wartość jest odczytywana przez składnik.

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

Wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracyjnych.

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

Aby odczytać inne pliki konfiguracji z `wwwroot` folderu do konfiguracji, użyj programu <xref:System.Net.Http.HttpClient> w celu uzyskania zawartości pliku. Poniższy przykład odczytuje plik konfiguracji ( `cars.json` ) do konfiguracji aplikacji.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> do `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration;
```

W `Program.Main` programie `Program.cs` zmodyfikuj istniejącą <xref:System.Net.Http.HttpClient> rejestrację usługi, aby korzystała z klienta programu w celu odczytania pliku:

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="custom-configuration-provider-with-ef-core"></a>Niestandardowy dostawca konfiguracji z EF Core

Niestandardowy dostawca konfiguracji z EF Core, który został pokazany w programie <xref:fundamentals/configuration/index#custom-configuration-provider> Works z Blazor WebAssembly aplikacjami.

> [!WARNING]
> Parametry połączenia bazy danych i bazy danych załadowane z Blazor WebAssembly aplikacjami nie są bezpieczne i nie powinny być używane do przechowywania poufnych danych.

Dodaj odwołania do pakietu dla [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) i [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) do pliku projektu aplikacji.

Dodaj klasy konfiguracji EF Core opisane w temacie <xref:fundamentals/configuration/index#custom-configuration-provider> .

Dodaj przestrzenie nazw dla <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> i <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> do `Program.cs` :

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

W `Program.Main` programie `Program.cs` :

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracyjnych.

`Pages/EFCoreConfig.razor`:

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a>Źródło konfiguracji pamięci

Poniższy przykład używa programu <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> w `Program.Main` celu dostarczenia dodatkowej konfiguracji.

Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> do `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

W `Program.Main` programie `Program.cs` :

```csharp
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

builder.Configuration.Add(memoryConfig);
```

Wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracyjnych.

`Pages/MemoryConfig.razor`:

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

Uzyskaj sekcję konfiguracji w kodzie C# za pomocą <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> . Poniższy przykład pobiera `wheels` sekcję konfiguracji w powyższym przykładzie:

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>Konfiguracja uwierzytelniania

Podaj konfigurację uwierzytelniania w pliku ustawień aplikacji.

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Załaduj konfigurację dostawcy za Identity pomocą <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> programu w programie `Program.Main` . Poniższy przykład ładuje konfigurację dla [dostawcy OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Konfiguracja rejestrowania

Dodaj odwołanie [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) do pakietu do pliku projektu aplikacji. W pliku ustawień aplikacji podaj konfigurację rejestrowania. Konfiguracja rejestrowania jest załadowana w `Program.Main` .

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

Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Logging?displayProperty=fullName> do `Program.cs` :

```csharp
using Microsoft.Extensions.Logging;
```

W `Program.Main` programie `Program.cs` :

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Konfiguracja konstruktora hostów

Odczytaj konfigurację konstruktora hosta z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> programu `Program.Main` .

W `Program.Main` programie `Program.cs` :

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Konfiguracja buforowana

Pliki konfiguracji są buforowane do użycia w trybie offline. Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia. Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:

* Użytkownicy mają buforowane wersje plików, które nadal są używane.
* W przypadku `service-worker.js` kompilacji i `service-worker-assets.js` plików programu PWA należy ponownie skompilować, który sygnalizuje aplikacji na następnej stronie online, że aplikacja została ponownie wdrożona.

Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .
