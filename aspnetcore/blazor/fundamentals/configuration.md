---
title: :::no-loc(Blazor):::Konfiguracja ASP.NET Core
author: guardrex
description: 'Dowiedz się więcej na temat konfiguracji :::no-loc(Blazor)::: aplikacji, w tym ustawień aplikacji, uwierzytelniania i konfiguracji rejestrowania.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 430ea2c764e068fe47353115cbeccd2c092617cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056013"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="b12a0-103">:::no-loc(Blazor):::Konfiguracja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b12a0-103">ASP.NET Core :::no-loc(Blazor)::: configuration</span></span>

> [!NOTE]
> <span data-ttu-id="b12a0-104">Ten temat ma zastosowanie do programu :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="b12a0-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="b12a0-105">Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core można znaleźć w temacie <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="b12a0-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="b12a0-106">:::no-loc(Blazor WebAssembly)::: ładuje domyślnie konfigurację z plików ustawień aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b12a0-106">:::no-loc(Blazor WebAssembly)::: loads configuration from app settings files by default:</span></span>

* `wwwroot/:::no-loc(appsettings.json):::`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="b12a0-107">Inni dostawcy konfiguracji zarejestrowani przez aplikację mogą także zapewnić konfigurację.</span><span class="sxs-lookup"><span data-stu-id="b12a0-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="b12a0-108">Nie wszyscy dostawcy lub funkcje dostawcy są odpowiednie dla :::no-loc(Blazor WebAssembly)::: aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b12a0-108">Not all providers or provider features are appropriate for :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* <span data-ttu-id="b12a0-109">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration): Dostawca nie jest obsługiwany dla tożsamości zarządzanej i identyfikatora aplikacji (identyfikator klienta) w scenariuszach tajnych klienta.</span><span class="sxs-lookup"><span data-stu-id="b12a0-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="b12a0-110">Identyfikator aplikacji ze tajemnicą klienta nie jest zalecany dla żadnej aplikacji ASP.NET Core, w szczególności :::no-loc(Blazor WebAssembly)::: aplikacji, ponieważ klucz tajny klienta nie może być zabezpieczony po stronie klienta w celu uzyskania dostępu do usługi.</span><span class="sxs-lookup"><span data-stu-id="b12a0-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially :::no-loc(Blazor WebAssembly)::: apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="b12a0-111">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): Dostawca nie jest odpowiedni dla :::no-loc(Blazor WebAssembly)::: aplikacji, ponieważ :::no-loc(Blazor WebAssembly)::: aplikacje nie są uruchamiane na serwerze na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="b12a0-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for :::no-loc(Blazor WebAssembly)::: apps because :::no-loc(Blazor WebAssembly)::: apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="b12a0-112">Konfiguracja w :::no-loc(Blazor WebAssembly)::: aplikacji jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="b12a0-112">Configuration in a :::no-loc(Blazor WebAssembly)::: app is visible to users.</span></span> <span data-ttu-id="b12a0-113">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="b12a0-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="b12a0-114">Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="b12a0-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="b12a0-115">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="b12a0-115">App settings configuration</span></span>

<span data-ttu-id="b12a0-116">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-116">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="b12a0-117">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b12a0-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="b12a0-118">Niestandardowy dostawca konfiguracji z EF Core</span><span class="sxs-lookup"><span data-stu-id="b12a0-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="b12a0-119">Niestandardowy dostawca konfiguracji z EF Core, który został pokazany w programie <xref:fundamentals/configuration/index#custom-configuration-provider> Works z :::no-loc(Blazor WebAssembly)::: aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="b12a0-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with :::no-loc(Blazor WebAssembly)::: apps.</span></span>

<span data-ttu-id="b12a0-120">Dodaj dostawcę konfiguracji przykładu do następującego kodu w `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="b12a0-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="b12a0-121">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b12a0-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="b12a0-122">Źródło konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="b12a0-122">Memory Configuration Source</span></span>

<span data-ttu-id="b12a0-123">Poniższy przykład używa, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> Aby podać dodatkową konfigurację:</span><span class="sxs-lookup"><span data-stu-id="b12a0-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="b12a0-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-124">`Program.Main`:</span></span>

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

<span data-ttu-id="b12a0-125">Wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b12a0-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="b12a0-126">Aby odczytać inne pliki konfiguracji z `wwwroot` folderu do konfiguracji, użyj programu <xref:System.Net.Http.HttpClient> w celu uzyskania zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="b12a0-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="b12a0-127">W przypadku korzystania z tego podejścia istniejąca <xref:System.Net.Http.HttpClient> rejestracja usługi może używać lokalnego klienta utworzonego do odczytu pliku, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b12a0-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="b12a0-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="b12a0-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="b12a0-130">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b12a0-130">Authentication configuration</span></span>

<span data-ttu-id="b12a0-131">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-131">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="b12a0-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="b12a0-133">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="b12a0-133">Logging configuration</span></span>

<span data-ttu-id="b12a0-134">Dodaj odwołanie do pakietu dla [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) :</span><span class="sxs-lookup"><span data-stu-id="b12a0-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="b12a0-135">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span><span class="sxs-lookup"><span data-stu-id="b12a0-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="b12a0-136">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-136">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

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

<span data-ttu-id="b12a0-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="b12a0-138">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="b12a0-138">Host builder configuration</span></span>

<span data-ttu-id="b12a0-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b12a0-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="b12a0-140">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="b12a0-140">Cached configuration</span></span>

<span data-ttu-id="b12a0-141">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="b12a0-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="b12a0-142">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="b12a0-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="b12a0-143">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="b12a0-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="b12a0-144">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="b12a0-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="b12a0-145">W przypadku `service-worker.js` kompilacji i `service-worker-assets.js` plików programu PWA należy ponownie skompilować, który sygnalizuje aplikacji na następnej stronie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="b12a0-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="b12a0-146">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="b12a0-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
