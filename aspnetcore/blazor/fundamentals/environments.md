---
title: BlazorŚrodowiska ASP.NET Core
author: guardrex
description: Dowiedz się więcej o środowiskach w programie Blazor , w tym o sposobie ustawiania środowiska Blazor WebAssembly aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056039"
---
# <a name="aspnet-core-no-locblazor-environments"></a>BlazorŚrodowiska ASP.NET Core

> [!NOTE]
> Ten temat ma zastosowanie do programu Blazor WebAssembly . Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core można znaleźć w temacie <xref:fundamentals/environments> .

Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane. Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.

Aplikacja hostowana Blazor WebAssembly Pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka. Wartość nagłówka to środowisko. Hostowana Blazor aplikacja i aplikacja serwera współużytkują to samo środowisko. Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, zobacz <xref:fundamentals/environments> .

W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne. Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.

W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego `web.config` pliku. Plik znajduje się `web.config` w `bin/Release/{TARGET FRAMEWORK}/publish` folderze:

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

Uzyskaj środowisko aplikacji w składniku, wprowadzając <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> i odczytując <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Właściwość:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Podczas uruchamiania program <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> ujawnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Właściwość może być używana podczas uruchamiania, gdy <xref:Microsoft.AspNetCore.Components.NavigationManager> Usługa jest niedostępna.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/environments>
