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
ms.openlocfilehash: a5ead59e467da331b585e8daefb1d7d259c7edba
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101051"
---
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="2fcc4-103">BlazorŚrodowiska ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fcc4-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="2fcc4-104">Ten temat ma zastosowanie do programu Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2fcc4-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="2fcc4-105">Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core, która opisuje podejścia do użycia dla Blazor Server aplikacji, znajduje się w temacie <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="2fcc4-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2fcc4-106">Podczas lokalnego uruchamiania aplikacji środowisko jest domyślnie opracowywane.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="2fcc4-107">Gdy aplikacja zostanie opublikowana, środowisko jest domyślne dla środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="2fcc4-108">Aplikacja po stronie klienta Blazor ( *`Client`* ) rozwiązania hostowanego Blazor WebAssembly określa środowisko z *`Server`* aplikacji rozwiązania za pośrednictwem oprogramowania pośredniczącego, które komunikuje środowisko z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="2fcc4-109">*`Server`* Aplikacja dodaje nagłówek o nazwie `blazor-environment` z środowiskiem jako wartość nagłówka.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="2fcc4-110">*`Client`* Aplikacja odczytuje nagłówek.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="2fcc4-111">*`Server`* Aplikacja rozwiązania jest aplikacją ASP.NET Core, więc więcej informacji na temat konfigurowania środowiska znajduje się w temacie <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="2fcc4-111">The *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2fcc4-112">W przypadku aplikacji autonomicznej Blazor WebAssembly uruchomionej lokalnie serwer programistyczny dodaje `blazor-environment` nagłówek, aby określić środowisko programistyczne.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="2fcc4-113">Aby określić środowisko dla innych środowisk hostingu, Dodaj `blazor-environment` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="2fcc4-114">W poniższym przykładzie dla usług IIS niestandardowy nagłówek ( `blazor-environment` ) zostanie dodany do opublikowanego `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="2fcc4-115">Plik znajduje się `web.config` w `bin/Release/{TARGET FRAMEWORK}/publish` folderze, gdzie symbol zastępczy `{TARGET FRAMEWORK}` jest platformą docelową:</span><span class="sxs-lookup"><span data-stu-id="2fcc4-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

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
> <span data-ttu-id="2fcc4-116">Aby użyć pliku niestandardowego `web.config` dla usług IIS, który nie jest zastępowany podczas publikowania aplikacji w `publish` folderze, zobacz <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="2fcc4-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="2fcc4-117">Uzyskaj środowisko aplikacji w składniku przez wstrzyknięcie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> i odczyt <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> właściwości.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="2fcc4-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="2fcc4-118">`Pages/ReadEnvironment.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

<span data-ttu-id="2fcc4-119">Podczas uruchamiania program <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> ujawnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> Właściwość przez, która umożliwia logikę specyficzną dla środowiska w kodzie konstruktora hosta.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="2fcc4-120">W `Program.Main` programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="2fcc4-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="2fcc4-121">Następujące wygodne metody rozszerzające, dzięki którym <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> można sprawdzać bieżące środowisko w zakresie programowania, produkcji, przemieszczania i niestandardowych nazw środowisk:</span><span class="sxs-lookup"><span data-stu-id="2fcc4-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="2fcc4-122">W `Program.Main` programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="2fcc4-122">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="2fcc4-123"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Właściwość może być używana podczas uruchamiania, gdy <xref:Microsoft.AspNetCore.Components.NavigationManager> Usługa jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="2fcc4-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fcc4-124">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2fcc4-124">Additional resources</span></span>

* <xref:fundamentals/environments>
