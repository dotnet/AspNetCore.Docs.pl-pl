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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485970"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="4ea21-103">BlazorKonfiguracja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ea21-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="4ea21-104">Ten temat ma zastosowanie do programu Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="4ea21-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="4ea21-105">Ogólne wskazówki dotyczące konfiguracji aplikacji ASP.NET Core można znaleźć w temacie <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="4ea21-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="4ea21-106">Blazor WebAssembly ładuje domyślnie konfigurację z następujących plików ustawień aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4ea21-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="4ea21-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="4ea21-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="4ea21-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, gdzie `{ENVIRONMENT}` symbol zastępczy to [środowisko środowiska uruchomieniowego](xref:fundamentals/environments)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ea21-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4ea21-109">Inni dostawcy konfiguracji zarejestrowani przez aplikację mogą także zapewnić konfigurację, ale nie wszyscy dostawcy lub funkcje dostawcy są odpowiednie dla Blazor WebAssembly aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4ea21-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="4ea21-110">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration): Dostawca nie jest obsługiwany dla tożsamości zarządzanej i identyfikatora aplikacji (identyfikator klienta) w scenariuszach tajnych klienta.</span><span class="sxs-lookup"><span data-stu-id="4ea21-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="4ea21-111">Identyfikator aplikacji ze tajemnicą klienta nie jest zalecany dla żadnej aplikacji ASP.NET Core, w szczególności Blazor WebAssembly aplikacji, ponieważ klucz tajny klienta nie może być zabezpieczony po stronie klienta w celu uzyskania dostępu do usługi Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="4ea21-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="4ea21-112">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): Dostawca nie jest odpowiedni dla Blazor WebAssembly aplikacji, ponieważ Blazor WebAssembly aplikacje nie są uruchamiane na serwerze na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="4ea21-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="4ea21-113">Konfiguracja w Blazor WebAssembly aplikacji jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="4ea21-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="4ea21-114">**Nie przechowuj wpisów tajnych aplikacji, poświadczeń ani innych poufnych danych w konfiguracji Blazor WebAssembly aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="4ea21-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="4ea21-115">Aby uzyskać więcej informacji na temat dostawców konfiguracji, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="4ea21-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="4ea21-116">Konfiguracja ustawień aplikacji</span><span class="sxs-lookup"><span data-stu-id="4ea21-116">App settings configuration</span></span>

<span data-ttu-id="4ea21-117">Konfiguracja w plikach ustawień aplikacji jest domyślnie ładowana.</span><span class="sxs-lookup"><span data-stu-id="4ea21-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="4ea21-118">W poniższym przykładzie wartość konfiguracji interfejsu użytkownika jest przechowywana w pliku ustawień aplikacji i automatycznie ładowana przez Blazor platformę.</span><span class="sxs-lookup"><span data-stu-id="4ea21-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="4ea21-119">Wartość jest odczytywana przez składnik.</span><span class="sxs-lookup"><span data-stu-id="4ea21-119">The value is read by a component.</span></span>

<span data-ttu-id="4ea21-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="4ea21-121">Wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="4ea21-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="4ea21-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="4ea21-123">Aby odczytać inne pliki konfiguracji z `wwwroot` folderu do konfiguracji, użyj programu <xref:System.Net.Http.HttpClient> w celu uzyskania zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="4ea21-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="4ea21-124">Poniższy przykład odczytuje plik konfiguracji ( `cars.json` ) do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ea21-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="4ea21-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="4ea21-126">Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> do `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="4ea21-127">W `Program.Main` programie `Program.cs` zmodyfikuj istniejącą <xref:System.Net.Http.HttpClient> rejestrację usługi, aby korzystała z klienta programu w celu odczytania pliku:</span><span class="sxs-lookup"><span data-stu-id="4ea21-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

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

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="4ea21-128">Niestandardowy dostawca konfiguracji z EF Core</span><span class="sxs-lookup"><span data-stu-id="4ea21-128">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="4ea21-129">Niestandardowy dostawca konfiguracji z EF Core, który został pokazany w programie <xref:fundamentals/configuration/index#custom-configuration-provider> Works z Blazor WebAssembly aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="4ea21-129">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

> [!WARNING]
> <span data-ttu-id="4ea21-130">Parametry połączenia bazy danych i bazy danych załadowane z Blazor WebAssembly aplikacjami nie są bezpieczne i nie powinny być używane do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="4ea21-130">Database connection strings and databases loaded with Blazor WebAssembly apps aren't secure and shouldn't be used to store sensitive data.</span></span>

<span data-ttu-id="4ea21-131">Dodaj odwołania do pakietu dla [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) i [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) do pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ea21-131">Add package references for [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) and [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) to the app's project file.</span></span>

<span data-ttu-id="4ea21-132">Dodaj klasy konfiguracji EF Core opisane w temacie <xref:fundamentals/configuration/index#custom-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="4ea21-132">Add the EF Core configuration classes described in <xref:fundamentals/configuration/index#custom-configuration-provider>.</span></span>

<span data-ttu-id="4ea21-133">Dodaj przestrzenie nazw dla <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> i <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> do `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-133">Add namespaces for <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> and <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="4ea21-134">W `Program.Main` programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-134">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="4ea21-135">Wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="4ea21-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="4ea21-136">`Pages/EFCoreConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-136">`Pages/EFCoreConfig.razor`:</span></span>

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

## <a name="memory-configuration-source"></a><span data-ttu-id="4ea21-137">Źródło konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="4ea21-137">Memory Configuration Source</span></span>

<span data-ttu-id="4ea21-138">Poniższy przykład używa programu <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> w `Program.Main` celu dostarczenia dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="4ea21-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="4ea21-139">Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> do `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-139">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="4ea21-140">W `Program.Main` programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-140">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="4ea21-141">Wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> wystąpienie do składnika w celu uzyskania dostępu do danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="4ea21-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="4ea21-142">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-142">`Pages/MemoryConfig.razor`:</span></span>

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

<span data-ttu-id="4ea21-143">Uzyskaj sekcję konfiguracji w kodzie C# za pomocą <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4ea21-143">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="4ea21-144">Poniższy przykład pobiera `wheels` sekcję konfiguracji w powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="4ea21-144">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="4ea21-145">Konfiguracja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="4ea21-145">Authentication configuration</span></span>

<span data-ttu-id="4ea21-146">Podaj konfigurację uwierzytelniania w pliku ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ea21-146">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="4ea21-147">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-147">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="4ea21-148">Załaduj konfigurację dostawcy za Identity pomocą <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> programu w programie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="4ea21-148">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="4ea21-149">Poniższy przykład ładuje konfigurację dla [dostawcy OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).</span><span class="sxs-lookup"><span data-stu-id="4ea21-149">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="4ea21-150">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-150">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="4ea21-151">Konfiguracja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="4ea21-151">Logging configuration</span></span>

<span data-ttu-id="4ea21-152">Dodaj odwołanie [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) do pakietu do pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ea21-152">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="4ea21-153">W pliku ustawień aplikacji podaj konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="4ea21-153">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="4ea21-154">Konfiguracja rejestrowania jest załadowana w `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="4ea21-154">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="4ea21-155">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4ea21-155">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="4ea21-156">Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Logging?displayProperty=fullName> do `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-156">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="4ea21-157">W `Program.Main` programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-157">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="4ea21-158">Konfiguracja konstruktora hostów</span><span class="sxs-lookup"><span data-stu-id="4ea21-158">Host builder configuration</span></span>

<span data-ttu-id="4ea21-159">Odczytaj konfigurację konstruktora hosta z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> programu `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="4ea21-159">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="4ea21-160">W `Program.Main` programie `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="4ea21-160">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="4ea21-161">Konfiguracja buforowana</span><span class="sxs-lookup"><span data-stu-id="4ea21-161">Cached configuration</span></span>

<span data-ttu-id="4ea21-162">Pliki konfiguracji są buforowane do użycia w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="4ea21-162">Configuration files are cached for offline use.</span></span> <span data-ttu-id="4ea21-163">Przy użyciu [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracji podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="4ea21-163">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="4ea21-164">Edytowanie plików konfiguracji między wdrożeniami nie ma żadnego skutku, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="4ea21-164">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="4ea21-165">Użytkownicy mają buforowane wersje plików, które nadal są używane.</span><span class="sxs-lookup"><span data-stu-id="4ea21-165">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="4ea21-166">W przypadku `service-worker.js` kompilacji i `service-worker-assets.js` plików programu PWA należy ponownie skompilować, który sygnalizuje aplikacji na następnej stronie online, że aplikacja została ponownie wdrożona.</span><span class="sxs-lookup"><span data-stu-id="4ea21-166">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="4ea21-167">Aby uzyskać więcej informacji o tym, jak aktualizacje w tle są obsługiwane przez PWAs, zobacz <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="4ea21-167">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
