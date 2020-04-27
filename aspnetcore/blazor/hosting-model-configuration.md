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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Konfiguracja modelu hostingu ASP.NET Core Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

W tym artykule opisano hostowanie konfiguracji modelu.

## <a name="blazor-webassembly"></a>Zestaw WebAssembly Blazor

### <a name="environment"></a>Środowisko

Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane. Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.

Hostowana aplikacja webassembly Blazor pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka. Wartość nagłówka to środowisko. Hostowana aplikacja Blazor i aplikacja serwera współużytkują to samo środowisko. Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, <xref:fundamentals/environments>Zobacz.

W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne. Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.

W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego pliku *Web. config* . Plik *Web. config* znajduje się w folderze *bin/Release/{Target Framework}/Publish* :

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
> Aby użyć niestandardowego pliku *Web. config* dla usług IIS, które nie są zastępowane podczas publikowania aplikacji w folderze *publikowania* , zobacz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.

Uzyskaj środowisko aplikacji w składniku, wprowadzając `IWebAssemblyHostEnvironment` i odczytując `Environment` Właściwość:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Podczas uruchamiania program `WebAssemblyHostBuilder` ujawnia `IWebAssemblyHostEnvironment` `HostEnvironment` Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Następujące wygodne metody rozszerzające umożliwiają sprawdzanie bieżącego środowiska pod kątem nazw środowisk deweloperskich, produkcyjnych, tymczasowych i niestandardowych:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* "Isenvironment (" {Nazwa środowiska} ")

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

`IWebAssemblyHostEnvironment.BaseAddress` Właściwość może być używana podczas uruchamiania, `NavigationManager` gdy usługa jest niedostępna.

### <a name="configuration"></a>Konfiguracja

Zestaw webassembly Blazor obsługuje konfigurację z:

* Domyślnie [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) dla plików ustawień aplikacji:
  * *wwwroot/appSettings. JSON*
  * *wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Inni [dostawcy konfiguracji](xref:fundamentals/configuration/index) zarejestrowani przez aplikację.

> [!WARNING]
> Konfiguracja w aplikacji Blazor webassembly jest widoczna dla użytkowników. **Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**

Aby uzyskać więcej informacji na temat dostawców konfiguracji <xref:fundamentals/configuration/index>, zobacz.

#### <a name="app-settings-configuration"></a>Konfiguracja ustawień aplikacji

*wwwroot/appSettings. JSON*:

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

#### <a name="provider-configuration"></a>Konfiguracja dostawcy

Poniższy przykład używa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> [dostawcy konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider) i dostarcza dodatkową konfigurację:

`Program.Main`:

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

Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:

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

#### <a name="authentication-configuration"></a>Konfiguracja uwierzytelniania

*wwwroot/appSettings. JSON*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Konfiguracja rejestrowania

*wwwroot/appSettings. JSON*:

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
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Konfiguracja konstruktora hostów

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Konfiguracja buforowana

Pliki konfiguracji są buforowane do użycia w trybie offline. Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia. Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:

* Użytkownicy mają buforowane wersje plików, które nadal są używane.
* Pliki *Service-Worker. js* i *Service-Worker-Assets. js* programu PWA muszą zostać ponownie skompilowane w ramach kompilacji, która sygnalizuje aplikacji w następnym trybie online, że aplikacja została ponownie wdrożona.

Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane <xref:blazor/progressive-web-app#background-updates>przez PWAs, zobacz.

### <a name="logging"></a>Rejestrowanie

Aby uzyskać informacje na temat obsługi rejestrowania w programie Blazor <xref:fundamentals/logging/index#create-logs-in-blazor>webassembly, zobacz.

## <a name="blazor-server"></a>Serwer Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Odzwierciedlanie stanu połączenia w interfejsie użytkownika

Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie. Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.

Aby dostosować interfejs użytkownika, zdefiniuj element `id` z elementu `components-reconnect-modal` na liście `<body>` *_Host. cshtml* Razor:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.

| Klasa CSS                       | Wskazuje&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Utracono połączenie. Klient próbuje ponownie nawiązać połączenie. Pokaż modalne. |
| `components-reconnect-hide`     | Aktywne połączenie zostanie ponownie nawiązane z serwerem. Ukryj modalne. |
| `components-reconnect-failed`   | Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci. Aby spróbować ponownie nawiązać połączenie `window.Blazor.reconnect()`, wywołaj polecenie. |
| `components-reconnect-rejected` | Odrzucono ponowne połączenie. Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony. Aby ponownie załadować aplikację, wywołaj `location.reload()`polecenie. Ten stan połączenia może skutkować tym, że:<ul><li>Wystąpił awaria w obwodzie po stronie serwera.</li><li>Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika. Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</li><li>Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</li></ul> |

### <a name="render-mode"></a>Tryb renderowania

Aplikacje serwera Blazor są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem. Ta konfiguracja jest ustawiana na stronie *_Host. cshtml* Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`Określa, czy składnik:

* Jest wstępnie renderowany na stronie.
* Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia aplikacji Blazor z poziomu agenta użytkownika.

| `RenderMode`        | Opis |
| ------------------- | ----------- |
| `ServerPrerendered` | Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| `Server`            | Renderuje znacznik dla aplikacji Blazor serwera. Dane wyjściowe ze składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| `Static`            | Renderuje składnik do statycznego kodu HTML. |

Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurowanie SignalR klienta dla Blazor aplikacji serwerowych

Czasami trzeba skonfigurować SignalR klienta używanego przez Blazor aplikacje serwera. Na przykład możesz chcieć skonfigurować rejestrowanie na kliencie, SignalR aby zdiagnozować problem z połączeniem.

Aby skonfigurować SignalR klienta w pliku *pages/_Host. cshtml* :

* Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.
* Wywoływanie `Blazor.start` i przekazywanie obiektu konfiguracji, który określa SignalR Konstruktor.

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

### <a name="logging"></a>Rejestrowanie

Aby uzyskać informacje Blazor na temat obsługi rejestrowania na <xref:fundamentals/logging/index#create-logs-in-blazor>serwerze, zobacz.
