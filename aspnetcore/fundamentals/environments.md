---
title: Używanie wielu środowisk na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kontrolować zachowanie aplikacji w wielu środowiskach w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656220"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Używanie wielu środowisk na platformie ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska wykonawczego przy użyciu zmiennej środowiskowej.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Środowiska

ASP.NET Core odczytuje zmienną `ASPNETCORE_ENVIRONMENT` środowiskową podczas uruchamiania aplikacji i przechowuje wartość w [pliku IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`można ustawić na dowolną wartość, ale trzy wartości są dostarczane przez framework:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>(domyślnie)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Wywołania [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` `Development`gdy jest ustawiona na .
* Wywołuje [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) gdy `ASPNETCORE_ENVIRONMENT` wartość jest ustawiona jeden z następujących czynności:

  * `Staging`
  * `Production`
  * `Staging_2`

[Pomocnik znaczników środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa `IHostingEnvironment.EnvironmentName` wartości, aby uwzględnić lub wykluczyć znaczniki w elemencie:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

W systemach Windows i macOS zmienne i wartości środowiskowe nie są rozróżniane. Zmienne i wartości środowiskowe systemu Linux są domyślnie **rozróżniane wielkość liter.**

### <a name="development"></a>Opracowywanie zawartości

Środowisko programistyczne można włączyć funkcje, które nie powinny być udostępniane w środowisku produkcyjnym. Na przykład szablony ASP.NET Core włączyć [stronę wyjątków dewelopera](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.

Środowisko lokalnego rozwoju maszyny można ustawić w pliku *Properties\launchSettings.json* projektu. Wartości środowiska ustawione w *launchSettings.json* zastępują wartości ustawione w środowisku systemowym.

Następujący JSON pokazuje trzy profile z pliku *launchSettings.json:*

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> Właściwość `applicationUrl` w *launchSettings.json* może określić listę adresów URL serwera. Użyj średnika między adresami URL na liście:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Po uruchomieniu aplikacji z [dotnet run,](/dotnet/core/tools/dotnet-run) `"commandName": "Project"` pierwszy profil z jest używany. Wartość `commandName` określa serwer www do uruchomienia. `commandName`może być jedną z następujących czynności:

* `IISExpress`
* `IIS`
* `Project`(który uruchamia Kestrel)

Po uruchomieniu aplikacji z [dotnet run:](/dotnet/core/tools/dotnet-run)

* *launchSettings.json* jest odczytywany, jeśli jest dostępny. `environmentVariables`w *launchSettings.json* zastępuj zmienne środowiskowe.
* Zostanie wyświetlone środowisko hostingu.

Poniższe dane wyjściowe pokazują aplikację uruchomiono z [dotnet run:](/dotnet/core/tools/dotnet-run)

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Karta **Debugowanie** właściwości projektu programu Visual Studio zawiera interfejs graficzny do edytowania pliku *launchSettings.json:*

![Ustawienie właściwości projektu Zmienne środowiskowe](environments/_static/project-properties-debug.png)

Zmiany wprowadzone w profilach projektów mogą zostać wprowadzone dopiero po ponownym uruchomieniu serwera sieci web. Pustułka musi zostać ponownie uruchomiona, zanim będzie mogła wykryć zmiany wprowadzone w jego środowisku.

> [!WARNING]
> *launchSettings.json* nie powinien przechowywać tajemnice. [Narzędzie Secret Manager](xref:security/app-secrets) może służyć do przechowywania wpisów tajnych dla rozwoju lokalnego.

Podczas korzystania z [programu Visual Studio Code,](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *vscode/launch.json.* Poniższy przykład ustawia `Development`środowisko na:

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

Plik *.vscode/launch.json* w projekcie nie jest odczytywany `dotnet run` podczas uruchamiania aplikacji w taki sam sposób, jak *właściwości/launchSettings.json*. Podczas uruchamiania aplikacji w rozwoju, który nie ma pliku *launchSettings.json,* albo ustawić środowisko ze zmienną środowiskową lub argument wiersza polecenia do `dotnet run` polecenia.

### <a name="production"></a>Produkcja

Środowisko produkcyjne należy skonfigurować tak, aby zmaksymalizować bezpieczeństwo, wydajność i niezawodność aplikacji. Niektóre typowe ustawienia, które różnią się od rozwoju obejmują:

* Buforowanie.
* Zasoby po stronie klienta są powiązane, zminifikowane i potencjalnie obsługiwane z sieci CDN.
* Strony błędów diagnostycznych wyłączone.
* Przyjazne strony błędów włączone.
* Rejestrowanie produkcji i monitorowanie włączone. Na przykład [usługa Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Ustawianie środowiska

Często jest przydatne do ustawienia określonego środowiska do testowania przy obliczu zmiennej środowiskowej lub ustawienia platformy. Jeśli środowisko nie jest ustawione, domyślnie jest to `Production`, co wyłącza większość funkcji debugowania. Metoda ustawiania środowiska zależy od systemu operacyjnego.

Po utworzeniu hosta ostatnie ustawienie środowiska odczytane przez aplikację określa środowisko aplikacji. Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.

### <a name="environment-variable-or-platform-setting"></a>Ustawienie zmiennej środowiskowej lub platformy

#### <a name="azure-app-service"></a>Azure App Service

Aby ustawić środowisko w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)wykonaj następujące czynności:

1. Wybierz aplikację z **bloku Usługi aplikacji.**
1. W grupie **Ustawienia** wybierz blok **Konfiguracja.**
1. Na karcie **Ustawienia aplikacji** wybierz pozycję Nowe **ustawienie aplikacji**.
1. W oknie **ustawień Dodaj/Edytuj** `ASPNETCORE_ENVIRONMENT` aplikację podaj **nazwę**. Dla **wartości**, zapewnić środowisko `Staging`(na przykład).
1. Zaznacz pole wyboru **Ustawienie gniazda wdrożenia,** jeśli ustawienie środowiska ma pozostać z bieżącym miejscem po zamianie gniazd wdrażania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.
1. Wybierz **przycisk OK,** aby zamknąć okno **ustawienia Dodaj/Edytuj aplikację.**
1. Wybierz **pozycję Zapisz** u góry bloku **Konfiguracja.**

Usługa Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji (zmiennej środowiskowej) w witrynie Azure portal.

#### <a name="windows"></a>Windows

Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja jest uruchomiona przy użyciu [dotnet run](/dotnet/core/tools/dotnet-run), używane są następujące polecenia:

**Wiersz polecenia**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Te polecenia są obowiązywać tylko dla bieżącego okna. Gdy okno jest zamknięte, `ASPNETCORE_ENVIRONMENT` ustawienie zostanie przywrócone do domyślnego ustawienia lub wartości maszyny.

Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:

* Otwórz zaawansowane **System** > **ustawienia systemowe** systemu Panelu `ASPNETCORE_ENVIRONMENT` **sterowania** > i dodaj lub edytuj wartość:

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiska podstawowego ASPNET](environments/_static/windows_aspnetcore_environment.png)

* Otwórz wiersz polecenia administracyjnego `setx` i użyj polecenia lub otwórz administracyjny wiersz polecenia programu PowerShell i użyj: `[Environment]::SetEnvironmentVariable`

  **Wiersz polecenia**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Przełącznik `/M` wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiona dla konta użytkownika.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Wartość `Machine` opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli wartość opcji zostanie `User`zmieniona na , zmienna środowiskowa jest ustawiana dla konta użytkownika.

Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona `dotnet run` globalnie, staje się skuteczna w każdym oknie polecenia otwartym po ustawieniu wartości.

**Web.config**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową za pomocą *pliku web.config,* zobacz sekcję *Ustawianie zmiennych środowiskowych* w pliku <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Plik projektu lub profil publikowania**

**W przypadku wdrożeń usług Windows IIS:** Dołącz `<EnvironmentName>` właściwość do [profilu publikowania (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu. Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pula aplikacji dla usług IIS**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej w serwisach IIS 10.0 lub nowszych), [ &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) zobacz *sekcję polecenia AppCmd.exe* w temacie Środowisko zmiennych środowiskowych. Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.

> [!IMPORTANT]
> Podczas hostowania aplikacji w użyczanych `ASPNETCORE_ENVIRONMENT` usług IIS i dodawania lub zmieniania zmiennej środowiskowej użyj jednego z następujących podejść, aby nowa wartość była pobierana przez aplikacje:
>
> * Wykonaj, `net stop was /y` `net start w3svc` po którym następuje wiersz polecenia.
> * Uruchom ponownie serwer.

#### <a name="macos"></a>macOS

Ustawienie bieżącego środowiska dla systemu macOS można wykonać w wierszu podczas uruchamiania aplikacji:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Alternatywnie, ustaw środowisko `export` przed uruchomieniem aplikacji:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *bashrc* lub *.bash_profile.* Edytuj plik za pomocą dowolnego edytora tekstu. Dodaj następującą instrukcję:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

W przypadku dystrybucji systemu `export` Linux użyj polecenia w wierszu polecenia dla ustawień zmiennych opartych na sesji i *bash_profile* pliku dla ustawień środowiska na poziomie komputera.

### <a name="set-the-environment-in-code"></a>Ustawianie środowiska w kodzie

Zadzwoń <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> podczas budowania hosta. Zobacz: <xref:fundamentals/host/generic-host#environmentname>.


### <a name="configuration-by-environment"></a>Konfiguracja według środowiska

Aby załadować konfigurację według środowiska, zalecamy:

* *appsettings* plików (*appsettings.{ środowiska}.json*). Zobacz: <xref:fundamentals/configuration/index#json-configuration-provider>.
* Zmienne środowiskowe (ustawione w każdym systemie, w którym aplikacja jest hostowana). Zobacz <xref:fundamentals/host/generic-host#environmentname> <xref:security/app-secrets#environment-variables>i .
* Secret Manager (tylko w środowisku programistycznym). Zobacz: <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Klasa i metody uruchamiania oparte na środowisku

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>Wstrzyknąć IWebHostEnvironment do Startup.Configure

Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup.Configure`. Takie podejście jest przydatne, gdy `Startup.Configure` aplikacja wymaga tylko dostosowania do kilku środowisk z minimalnymi różnicami kodu na środowisko.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Wstrzyknąć IWebHostEnvironment do startup klasy

Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` konstruktora. Takie podejście jest przydatne, gdy `Startup` aplikacja wymaga skonfigurowania tylko dla kilku środowisk z minimalnymi różnicami kodu na środowisko.

W poniższym przykładzie:

* Środowisko odbywa się `_env` w terenie.
* `_env`jest używany `ConfigureServices` `Configure` w i zastosować konfigurację uruchamiania na podstawie środowiska aplikacji.

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```
### <a name="startup-class-conventions"></a>Konwencje klasy uruchamiania

Po uruchomieniu aplikacji ASP.NET Core, [startup klasy](xref:fundamentals/startup) bootstraps aplikacji. Aplikacja może definiować oddzielne `Startup` klasy dla `StartupDevelopment`różnych środowisk (na przykład ). Odpowiednia `Startup` klasa jest wybierana w czasie wykonywania. Klasa, której sufiks nazwy pasuje do bieżącego środowiska, jest traktowana priorytetowo. Jeśli pasująca `Startup{EnvironmentName}` klasa nie zostanie `Startup` znaleziona, klasa jest używana. Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.

Aby zaimplementować klasy oparte na `Startup` środowisku, należy utworzyć `Startup{EnvironmentName}` klasę dla każdego używanego środowiska i klasę rezerwową: `Startup`

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Użyj [przeciążenia UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) który akceptuje nazwę zestawu:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Konwencje metody uruchamiania

[Konfigurowanie](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [konfigurowanie Usługi](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) obsługują wersje formularza `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`specyficzne dla środowiska i . Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska wykonawczego przy użyciu zmiennej środowiskowej.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Środowiska

ASP.NET Core odczytuje zmienną `ASPNETCORE_ENVIRONMENT` środowiskową podczas uruchamiania aplikacji i przechowuje wartość w [pliku IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`można ustawić na dowolną wartość, ale trzy wartości są dostarczane przez framework:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(domyślnie)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Wywołania [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` `Development`gdy jest ustawiona na .
* Wywołuje [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) gdy `ASPNETCORE_ENVIRONMENT` wartość jest ustawiona jeden z następujących czynności:

  * `Staging`
  * `Production`
  * `Staging_2`

[Pomocnik znaczników środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa `IHostingEnvironment.EnvironmentName` wartości, aby uwzględnić lub wykluczyć znaczniki w elemencie:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

W systemach Windows i macOS zmienne i wartości środowiskowe nie są rozróżniane. Zmienne i wartości środowiskowe systemu Linux są domyślnie **rozróżniane wielkość liter.**

### <a name="development"></a>Opracowywanie zawartości

Środowisko programistyczne można włączyć funkcje, które nie powinny być udostępniane w środowisku produkcyjnym. Na przykład szablony ASP.NET Core włączyć [stronę wyjątków dewelopera](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.

Środowisko lokalnego rozwoju maszyny można ustawić w pliku *Properties\launchSettings.json* projektu. Wartości środowiska ustawione w *launchSettings.json* zastępują wartości ustawione w środowisku systemowym.

Następujący JSON pokazuje trzy profile z pliku *launchSettings.json:*

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> Właściwość `applicationUrl` w *launchSettings.json* może określić listę adresów URL serwera. Użyj średnika między adresami URL na liście:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Po uruchomieniu aplikacji z [dotnet run,](/dotnet/core/tools/dotnet-run) `"commandName": "Project"` pierwszy profil z jest używany. Wartość `commandName` określa serwer www do uruchomienia. `commandName`może być jedną z następujących czynności:

* `IISExpress`
* `IIS`
* `Project`(który uruchamia Kestrel)

Po uruchomieniu aplikacji z [dotnet run:](/dotnet/core/tools/dotnet-run)

* *launchSettings.json* jest odczytywany, jeśli jest dostępny. `environmentVariables`w *launchSettings.json* zastępuj zmienne środowiskowe.
* Zostanie wyświetlone środowisko hostingu.

Poniższe dane wyjściowe pokazują aplikację uruchomiono z [dotnet run:](/dotnet/core/tools/dotnet-run)

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Karta **Debugowanie** właściwości projektu programu Visual Studio zawiera interfejs graficzny do edytowania pliku *launchSettings.json:*

![Ustawienie właściwości projektu Zmienne środowiskowe](environments/_static/project-properties-debug.png)

Zmiany wprowadzone w profilach projektów mogą zostać wprowadzone dopiero po ponownym uruchomieniu serwera sieci web. Pustułka musi zostać ponownie uruchomiona, zanim będzie mogła wykryć zmiany wprowadzone w jego środowisku.

> [!WARNING]
> *launchSettings.json* nie powinien przechowywać tajemnice. [Narzędzie Secret Manager](xref:security/app-secrets) może służyć do przechowywania wpisów tajnych dla rozwoju lokalnego.

Podczas korzystania z [programu Visual Studio Code,](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *vscode/launch.json.* Poniższy przykład ustawia `Development`środowisko na:

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

Plik *.vscode/launch.json* w projekcie nie jest odczytywany `dotnet run` podczas uruchamiania aplikacji w taki sam sposób, jak *właściwości/launchSettings.json*. Podczas uruchamiania aplikacji w rozwoju, który nie ma pliku *launchSettings.json,* albo ustawić środowisko ze zmienną środowiskową lub argument wiersza polecenia do `dotnet run` polecenia.

### <a name="production"></a>Produkcja

Środowisko produkcyjne należy skonfigurować tak, aby zmaksymalizować bezpieczeństwo, wydajność i niezawodność aplikacji. Niektóre typowe ustawienia, które różnią się od rozwoju obejmują:

* Buforowanie.
* Zasoby po stronie klienta są powiązane, zminifikowane i potencjalnie obsługiwane z sieci CDN.
* Strony błędów diagnostycznych wyłączone.
* Przyjazne strony błędów włączone.
* Rejestrowanie produkcji i monitorowanie włączone. Na przykład [usługa Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Ustawianie środowiska

Często jest przydatne do ustawienia określonego środowiska do testowania przy obliczu zmiennej środowiskowej lub ustawienia platformy. Jeśli środowisko nie jest ustawione, domyślnie jest to `Production`, co wyłącza większość funkcji debugowania. Metoda ustawiania środowiska zależy od systemu operacyjnego.

Po utworzeniu hosta ostatnie ustawienie środowiska odczytane przez aplikację określa środowisko aplikacji. Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.

### <a name="environment-variable-or-platform-setting"></a>Ustawienie zmiennej środowiskowej lub platformy

#### <a name="azure-app-service"></a>Azure App Service

Aby ustawić środowisko w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)wykonaj następujące czynności:

1. Wybierz aplikację z **bloku Usługi aplikacji.**
1. W grupie **Ustawienia** wybierz blok **Konfiguracja.**
1. Na karcie **Ustawienia aplikacji** wybierz pozycję Nowe **ustawienie aplikacji**.
1. W oknie **ustawień Dodaj/Edytuj** `ASPNETCORE_ENVIRONMENT` aplikację podaj **nazwę**. Dla **wartości**, zapewnić środowisko `Staging`(na przykład).
1. Zaznacz pole wyboru **Ustawienie gniazda wdrożenia,** jeśli ustawienie środowiska ma pozostać z bieżącym miejscem po zamianie gniazd wdrażania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.
1. Wybierz **przycisk OK,** aby zamknąć okno **ustawienia Dodaj/Edytuj aplikację.**
1. Wybierz **pozycję Zapisz** u góry bloku **Konfiguracja.**

Usługa Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji (zmiennej środowiskowej) w witrynie Azure portal.

#### <a name="windows"></a>Windows

Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja jest uruchomiona przy użyciu [dotnet run](/dotnet/core/tools/dotnet-run), używane są następujące polecenia:

**Wiersz polecenia**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Te polecenia są obowiązywać tylko dla bieżącego okna. Gdy okno jest zamknięte, `ASPNETCORE_ENVIRONMENT` ustawienie zostanie przywrócone do domyślnego ustawienia lub wartości maszyny.

Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:

* Otwórz zaawansowane **System** > **ustawienia systemowe** systemu Panelu `ASPNETCORE_ENVIRONMENT` **sterowania** > i dodaj lub edytuj wartość:

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiska podstawowego ASPNET](environments/_static/windows_aspnetcore_environment.png)

* Otwórz wiersz polecenia administracyjnego `setx` i użyj polecenia lub otwórz administracyjny wiersz polecenia programu PowerShell i użyj: `[Environment]::SetEnvironmentVariable`

  **Wiersz polecenia**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Przełącznik `/M` wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiona dla konta użytkownika.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Wartość `Machine` opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli wartość opcji zostanie `User`zmieniona na , zmienna środowiskowa jest ustawiana dla konta użytkownika.

Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona `dotnet run` globalnie, staje się skuteczna w każdym oknie polecenia otwartym po ustawieniu wartości.

**Web.config**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową za pomocą *pliku web.config,* zobacz sekcję *Ustawianie zmiennych środowiskowych* w pliku <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Plik projektu lub profil publikowania**

**W przypadku wdrożeń usług Windows IIS:** Dołącz `<EnvironmentName>` właściwość do [profilu publikowania (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu. Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Pula aplikacji dla usług IIS**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej w serwisach IIS 10.0 lub nowszych), [ &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) zobacz *sekcję polecenia AppCmd.exe* w temacie Środowisko zmiennych środowiskowych. Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.

> [!IMPORTANT]
> Podczas hostowania aplikacji w użyczanych `ASPNETCORE_ENVIRONMENT` usług IIS i dodawania lub zmieniania zmiennej środowiskowej użyj jednego z następujących podejść, aby nowa wartość była pobierana przez aplikacje:
>
> * Wykonaj, `net stop was /y` `net start w3svc` po którym następuje wiersz polecenia.
> * Uruchom ponownie serwer.

#### <a name="macos"></a>macOS

Ustawienie bieżącego środowiska dla systemu macOS można wykonać w wierszu podczas uruchamiania aplikacji:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Alternatywnie, ustaw środowisko `export` przed uruchomieniem aplikacji:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *bashrc* lub *.bash_profile.* Edytuj plik za pomocą dowolnego edytora tekstu. Dodaj następującą instrukcję:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

W przypadku dystrybucji systemu `export` Linux użyj polecenia w wierszu polecenia dla ustawień zmiennych opartych na sesji i *bash_profile* pliku dla ustawień środowiska na poziomie komputera.

### <a name="set-the-environment-in-code"></a>Ustawianie środowiska w kodzie

Zadzwoń <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> podczas budowania hosta. Zobacz: <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Konfiguracja według środowiska

Aby załadować konfigurację według środowiska, zalecamy:

* *appsettings* plików (*appsettings.{ środowiska}.json*). Zobacz: <xref:fundamentals/configuration/index#json-configuration-provider>.
* Zmienne środowiskowe (ustawione w każdym systemie, w którym aplikacja jest hostowana). Zobacz <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables>i .
* Secret Manager (tylko w środowisku programistycznym). Zobacz: <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Klasa i metody uruchamiania oparte na środowisku

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Wstrzyknąć IHostingEnvironment do Startup.Configure

Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure`. Takie podejście jest przydatne, gdy `Startup.Configure` aplikacja wymaga tylko konfigurowania tylko dla kilku środowisk z minimalnymi różnicami kodu na środowisko.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Wstrzyknąć IHostingEnvironment do klasy Uruchamiani

Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup` konstruktora i przypisać `Startup` usługę do pola do użytku w całej klasie. Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania tylko dla kilku środowisk z minimalnymi różnicami kodu na środowisko.

W poniższym przykładzie:

* Środowisko odbywa się `_env` w terenie.
* `_env`jest używany `ConfigureServices` `Configure` w i zastosować konfigurację uruchamiania na podstawie środowiska aplikacji.

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a>Konwencje klasy uruchamiania

Po uruchomieniu aplikacji ASP.NET Core, [startup klasy](xref:fundamentals/startup) bootstraps aplikacji. Aplikacja może definiować oddzielne `Startup` klasy dla `StartupDevelopment`różnych środowisk (na przykład ). Odpowiednia `Startup` klasa jest wybierana w czasie wykonywania. Klasa, której sufiks nazwy pasuje do bieżącego środowiska, jest traktowana priorytetowo. Jeśli pasująca `Startup{EnvironmentName}` klasa nie zostanie `Startup` znaleziona, klasa jest używana. Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.

Aby zaimplementować klasy oparte na `Startup` środowisku, należy utworzyć `Startup{EnvironmentName}` klasę dla każdego używanego środowiska i klasę rezerwową: `Startup`

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

Użyj [przeciążenia UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) który akceptuje nazwę zestawu:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Konwencje metody uruchamiania

[Konfigurowanie](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [konfigurowanie Usługi](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) obsługują wersje formularza `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`specyficzne dla środowiska i . Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
