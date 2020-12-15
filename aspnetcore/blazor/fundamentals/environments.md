---
title: BlazorŚrodowiska ASP.NET Core
author: guardrex
description: Dowiedz się więcej o środowiskach w programie Blazor , w tym o sposobie ustawiania środowiska Blazor WebAssembly aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 9ba23753df1726ee4c8a9802e1a1260ef7cf6fa5
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506958"
---
# <a name="aspnet-core-no-locblazor-environments"></a>BlazorŚrodowiska ASP.NET Core

> [!NOTE]
> Ten temat ma zastosowanie do programu Blazor WebAssembly . Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core, która opisuje podejścia do użycia dla Blazor Server aplikacji, znajduje się w temacie <xref:fundamentals/environments> .

Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane. Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.

Aplikacja po stronie klienta Blazor ( *`Client`* ) rozwiązania hostowanego Blazor WebAssembly określa środowisko z *`Server`* aplikacji rozwiązania za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką. *`Server`* Aplikacja dodaje nagłówek o nazwie `blazor-environment` z środowiskiem jako wartość nagłówka. *`Client`* Aplikacja odczytuje nagłówek. *`Server`* Aplikacja rozwiązania jest aplikacją ASP.NET Core, więc więcej informacji na temat konfigurowania środowiska znajduje się w temacie <xref:fundamentals/environments> .

W przypadku aplikacji autonomicznej Blazor WebAssembly uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne. Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.

W poniższym przykładzie dla usług IIS niestandardowy nagłówek ( `blazor-environment` ) zostanie dodany do opublikowanego `web.config` pliku. Plik znajduje się `web.config` w `bin/Release/{TARGET FRAMEWORK}/publish` folderze, gdzie symbol zastępczy `{TARGET FRAMEWORK}` jest platformą docelową:

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
> Aby użyć pliku niestandardowego `web.config` dla usług IIS, który nie jest zastępowany podczas publikowania aplikacji w `publish` folderze, zobacz <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .

Uzyskaj środowisko aplikacji w składniku przez wstrzyknięcie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> i odczyt <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> właściwości.

`Pages/ReadEnvironment.razor`:

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

Podczas uruchamiania program <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> ujawnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> Właściwość przez, która umożliwia logikę specyficzną dla środowiska w kodzie konstruktora hosta.

W `Program.Main` programie `Program.cs` :

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Następujące wygodne metody rozszerzające, dzięki którym <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> można sprawdzać bieżące środowisko w zakresie programowania, produkcji, przemieszczania i niestandardowych nazw środowisk:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

W `Program.Main` programie `Program.cs` :

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Właściwość może być używana podczas uruchamiania, gdy <xref:Microsoft.AspNetCore.Components.NavigationManager> Usługa jest niedostępna.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/environments>
