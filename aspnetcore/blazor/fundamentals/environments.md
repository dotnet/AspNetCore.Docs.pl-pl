---
title: BlazorŚrodowiska ASP.NET Core
author: guardrex
description: 'Dowiedz się więcej o środowiskach w programie Blazor , w tym o sposobie ustawiania środowiska Blazor WebAssembly aplikacji.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056039"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="00fa8-103">BlazorŚrodowiska ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00fa8-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="00fa8-104">Ten temat ma zastosowanie do programu Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="00fa8-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="00fa8-105">Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core można znaleźć w temacie <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="00fa8-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="00fa8-106">Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane.</span><span class="sxs-lookup"><span data-stu-id="00fa8-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="00fa8-107">Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="00fa8-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="00fa8-108">Aplikacja hostowana Blazor WebAssembly Pobiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką przez dodanie `blazor-environment` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="00fa8-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="00fa8-109">Wartość nagłówka to środowisko.</span><span class="sxs-lookup"><span data-stu-id="00fa8-109">The value of the header is the environment.</span></span> <span data-ttu-id="00fa8-110">Hostowana Blazor aplikacja i aplikacja serwera współużytkują to samo środowisko.</span><span class="sxs-lookup"><span data-stu-id="00fa8-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="00fa8-111">Aby uzyskać więcej informacji, w tym o sposobie konfigurowania środowiska, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="00fa8-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="00fa8-112">W przypadku aplikacji autonomicznej uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne.</span><span class="sxs-lookup"><span data-stu-id="00fa8-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="00fa8-113">Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="00fa8-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="00fa8-114">W poniższym przykładzie dla usług IIS Dodaj nagłówek niestandardowy do opublikowanego `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="00fa8-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="00fa8-115">Plik znajduje się `web.config` w `bin/Release/{TARGET FRAMEWORK}/publish` folderze:</span><span class="sxs-lookup"><span data-stu-id="00fa8-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="00fa8-116">Aby użyć pliku niestandardowego `web.config` dla usług IIS, który nie jest zastępowany podczas publikowania aplikacji w `publish` folderze, zobacz <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="00fa8-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="00fa8-117">Uzyskaj środowisko aplikacji w składniku, wprowadzając <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> i odczytując <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Właściwość:</span><span class="sxs-lookup"><span data-stu-id="00fa8-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="00fa8-118">Podczas uruchamiania program <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> ujawnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> Właściwość przez, co umożliwia deweloperom zdefiniowanie w kodzie logiki specyficznej dla środowiska:</span><span class="sxs-lookup"><span data-stu-id="00fa8-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="00fa8-119">Następujące wygodne metody rozszerzające umożliwiają sprawdzanie bieżącego środowiska pod kątem nazw środowisk deweloperskich, produkcyjnych, tymczasowych i niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="00fa8-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="00fa8-120"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Właściwość może być używana podczas uruchamiania, gdy <xref:Microsoft.AspNetCore.Components.NavigationManager> Usługa jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="00fa8-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00fa8-121">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="00fa8-121">Additional resources</span></span>

* <xref:fundamentals/environments>
