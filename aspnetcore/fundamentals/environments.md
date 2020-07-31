---
title: Używanie wielu środowisk na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kontrolować zachowanie aplikacji w wielu środowiskach w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330663"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Używanie wielu środowisk na platformie ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)

ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego przy użyciu zmiennej środowiskowej.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Środowiska

Aby określić środowisko uruchomieniowe, ASP.NET Core odczytuje z następujących zmiennych środowiskowych:

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. `ASPNETCORE_ENVIRONMENT`gdy <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> jest wywoływana. Domyślne wywołania szablonów aplikacji sieci Web ASP.NET Core `ConfigureWebHostDefaults` . `ASPNETCORE_ENVIRONMENT`Wartość jest zastąpień `DOTNET_ENVIRONMENT` .

`IHostEnvironment.EnvironmentName`można ustawić na dowolną wartość, ale następujące wartości są dostarczane przez platformę:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>: [launchSettings.jsw](#lsj) zestawach plików `ASPNETCORE_ENVIRONMENT` na `Development` komputerze lokalnym.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>: Wartość domyślna if `DOTNET_ENVIRONMENT` i `ASPNETCORE_ENVIRONMENT` nie została ustawiona.

Następujący kod:

* Wywołuje [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` , gdy jest ustawiony na `Development` .
* Wywołuje [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , gdy wartość `ASPNETCORE_ENVIRONMENT` jest ustawiona na `Staging` , `Production` lub `Staging_2` .
* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>Wprowadza do `Startup.Configure` . Takie podejście jest przydatne, gdy aplikacja wymaga tylko dostosowania `Startup.Configure` w kilku środowiskach z minimalnymi różnicami kodu na środowisko.
* Jest podobny do kodu wygenerowanego przez szablony ASP.NET Core.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

[Pomocnik tagu środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa wartości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) do dołączania lub wykluczania znaczników do elementu:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

[Strona informacje](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) zawiera poprzedni znacznik i wyświetla wartość `IWebHostEnvironment.EnvironmentName` .

W systemach Windows i macOS, zmienne środowiskowe i wartości nie uwzględniają wielkości liter. Zmienne środowiskowe systemu Linux i wartości domyślnie **uwzględniają wielkość** liter.

### <a name="create-environmentssample"></a>Utwórz EnvironmentsSample

[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) używany w tym dokumencie jest oparty na Razor projekcie stron o nazwie *EnvironmentsSample*.

Poniższy kod tworzy i uruchamia aplikację sieci Web o nazwie *EnvironmentsSample*:

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

Po uruchomieniu aplikacji zostaną wyświetlone następujące dane wyjściowe:

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a>Programowanie i launchSettings.jsna

Środowisko programistyczne może włączyć funkcje, które nie powinny być ujawnione w środowisku produkcyjnym. Na przykład szablony ASP.NET Core umożliwiają [stronie wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.

Środowisko do tworzenia maszyn lokalnych można ustawić w *Properties\launchSettings.jsw* pliku projektu. Wartości środowiskowe ustawione w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.

*launchSettings.jsw* pliku:

* Jest używana tylko na lokalnym komputerze deweloperskim.
* Nie jest wdrożony.
* zawiera ustawienia profilu.

Poniższy kod JSON przedstawia *launchSettings.jsw* pliku dla ASP.NET Core sieci Web o nazwie *EnvironmentsSample* utworzone w programie Visual Studio lub `dotnet new` :

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

Poprzedzające znaczniki zawierają dwa profile:

* `IIS Express`: Profil domyślny używany podczas uruchamiania aplikacji z programu Visual Studio. `"commandName"`Klucz ma wartość `"IISExpress"` , dlatego [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) jest serwerem sieci Web. Można ustawić profil uruchamiania dla projektu lub dowolnego innego profilu. Na przykład na poniższym obrazie wybranie nazwy projektu powoduje uruchomienie [serwera sieci Web Kestrel](xref:fundamentals/servers/kestrel).

  ![IIS Express uruchamiania w menu](environments/_static/iisx2.png)
* `EnvironmentsSample`: Nazwa profilu jest nazwą projektu. Ten profil jest używany domyślnie podczas uruchamiania aplikacji za pomocą programu `dotnet run` .  `"commandName"`Klucz ma wartość, w `"Project"` związku z czym jest uruchamiany [serwer sieci Web Kestrel](xref:fundamentals/servers/kestrel) .

Wartość `commandName` może określać serwer sieci Web do uruchomienia. `commandName`może być jedną z następujących czynności:

* `IISExpress`: Uruchamia IIS Express.
* `IIS`: Nie uruchomiono żadnego serwera sieci Web. Usługi IIS powinny być dostępne.
* `Project`: Uruchamia Kestrel.

Karta **debugowanie** właściwości projektu programu Visual Studio zawiera graficzny interfejs użytkownika umożliwiający edytowanie *launchSettings.js* pliku. Zmiany wprowadzone w profilach projektu mogą zacząć obowiązywać dopiero po ponownym uruchomieniu serwera sieci Web. Kestrel musi zostać uruchomiona ponownie, zanim będzie mógł wykryć zmiany wprowadzone w środowisku.

![Ustawienia właściwości projektu zmienne środowiskowe](environments/_static/project-properties-debug.png)

Następujący *launchSettings.js* pliku zawiera wiele profilów:

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

Można wybrać profile:

* Z interfejsu użytkownika programu Visual Studio.
* Użycie [`dotnet run`](/dotnet/core/tools/dotnet-run) polecenia w powłoce poleceń z `--launch-profile` opcją ustawioną na nazwę profilu. *To podejście obsługuje tylko profile Kestrel.*

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> *launchSettings.js* nie należy przechowywać wpisów tajnych. [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets) można używać do przechowywania wpisów tajnych na potrzeby lokalnego tworzenia oprogramowania.

W przypadku korzystania z [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *. programu vscode/launch.js* . W poniższym przykładzie są ustawiane [zmienne środowiskowe wartości konfiguracji hosta](xref:fundamentals/host/web-host#host-configuration-values):

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

Plik *. programu vscode/launch.jsw* pliku jest używany tylko przez Visual Studio Code.

### <a name="production"></a>Produkcja

Środowisko produkcyjne powinno być skonfigurowane w celu zmaksymalizowania zabezpieczeń, [wydajności](xref:performance/performance-best-practices)i niezawodności aplikacji. Niektóre typowe ustawienia, które różnią się od programowania, to m.in.:

* [Buforowanie](xref:performance/caching/memory).
* Zasoby po stronie klienta są powiązane, zminimalizowanego i mogą być obsługiwane przez sieć CDN.
* Wyłączono strony błędów diagnostyki.
* Włączono przyjazne strony błędów.
* Włączono [Rejestrowanie](xref:fundamentals/logging/index) i monitorowanie produkcji. Na przykład przy użyciu [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Ustawianie środowiska

Często warto ustawić określone środowisko do testowania przy użyciu zmiennej środowiskowej lub ustawienia platformy. Jeśli środowisko nie jest ustawione, domyślnie jest to `Production` , co spowoduje wyłączenie większości funkcji debugowania. Metoda ustawiania środowiska zależy od systemu operacyjnego.

Po skompilowaniu hosta ostatnie ustawienie środowiska odczytywane przez aplikację określa środowisko aplikacji. Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.

Na [stronie informacje](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) jest wyświetlana wartość `IWebHostEnvironment.EnvironmentName` .

### <a name="azure-app-service"></a>Azure App Service

Aby ustawić środowisko w [Azure App Service](https://azure.microsoft.com/services/app-service/), wykonaj następujące czynności:

1. Wybierz aplikację z bloku **App Services** .
1. W grupie **Ustawienia** wybierz blok **Konfiguracja** .
1. Na karcie **Ustawienia aplikacji** wybierz pozycję **nowe ustawienie aplikacji**.
1. W oknie **Dodawanie/Edytowanie ustawienia aplikacji** Podaj `ASPNETCORE_ENVIRONMENT` **nazwę**. W polu **wartość**Podaj środowisko (na przykład `Staging` ).
1. Zaznacz pole wyboru **ustawienie miejsca wdrożenia** , jeśli chcesz, aby ustawienie środowiska pozostawało w bieżącym gnieździe w przypadku wymiany miejsc wdrożenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.
1. Wybierz **przycisk OK** , aby zamknąć okno **Dodawanie/Edytowanie ustawienia aplikacji** .
1. Wybierz pozycję **Zapisz** w górnej części bloku **Konfiguracja** .

Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji w Azure Portal.

### <a name="windows"></a>Windows

Wartości środowiskowe w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.

Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja zostanie uruchomiona przy użyciu [uruchomienia dotnet](/dotnet/core/tools/dotnet-run), są używane następujące polecenia:

**Wiersz polecenia**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**Program PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

Poprzednie polecenie ustawia `ASPNETCORE_ENVIRONMENT` tylko dla procesów uruchomionych z tego okna poleceń.

Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:

* Otwórz **Panel sterowania** > **system** > **Zaawansowane ustawienia systemowe** i Dodaj lub Edytuj `ASPNETCORE_ENVIRONMENT` wartość:

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiskowa ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Otwórz wiersz polecenia z uprawnieniami administracyjnymi i Użyj `setx` polecenia lub Otwórz wiersz polecenia administracyjnych programu PowerShell i Użyj `[Environment]::SetEnvironmentVariable` :

  **Wiersz polecenia**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  `/M`Przełącznik wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiana dla konta użytkownika.

  **Program PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  `Machine`Wartość opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli wartość opcji jest zmieniana na `User` , zmienna środowiskowa jest ustawiana dla konta użytkownika.

Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona globalnie, obowiązuje `dotnet run` w każdym oknie polecenia otwartym po ustawieniu wartości. Wartości środowiskowe w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.

**web.config**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową o *web.config*, zobacz sekcję *Ustawianie zmiennych środowiskowych* w temacie <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .

**Plik projektu lub profil publikacji**

**W przypadku wdrożeń usług Windows IIS:** Uwzględnij `<EnvironmentName>` Właściwość w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub w pliku projektu. To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Na pulę aplikacji IIS**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej przez usługi IIS 10,0 lub nowszej), zobacz sekcję *polecenieAppCmd.exe* w temacie [zmienne środowiskowe &lt; &gt; environmentVariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) . Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.

Podczas hostowania aplikacji w usługach IIS i dodawania lub zmieniania `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej Użyj jednego z poniższych metod, aby nowa wartość była pobierana przez aplikacje:

* Wykonaj `net stop was /y` , a następnie `net start w3svc` z wiersza polecenia.
* Uruchom ponownie serwer.

#### <a name="macos"></a>macOS

Ustawienie bieżącego środowiska dla macOS można wykonać w trybie online podczas uruchamiania aplikacji:

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

Alternatywnie można ustawić środowisko przy użyciu programu `export` przed uruchomieniem aplikacji:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *. bashrc* lub *. bash_profile* . Edytuj plik przy użyciu dowolnego edytora tekstu. Dodaj następującą instrukcję:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

W przypadku dystrybucji systemu Linux Użyj `export` polecenia z wiersza polecenia w przypadku ustawień zmiennych opartych na sesji i pliku *bash_profile* dla ustawień środowiska na poziomie komputera.

### <a name="set-the-environment-in-code"></a>Ustawianie środowiska w kodzie

Wywołanie <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> podczas kompilowania hosta. Zobacz: <xref:fundamentals/host/generic-host#environmentname>.

### <a name="configuration-by-environment"></a>Konfiguracja według środowiska

Aby załadować konfigurację według środowiska, zobacz <xref:fundamentals/configuration/index#json-configuration-provider> .

## <a name="environment-based-startup-class-and-methods"></a>Klasy i metody uruchamiania oparte na środowisku

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Wsuń IWebHostEnvironment do klasy startowej

Wsuń <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` konstruktora. Takie podejście jest przydatne, gdy aplikacja wymaga konfiguracji `Startup` tylko dla kilku środowisk z minimalnymi różnicami w kodzie dla danego środowiska.

W poniższym przykładzie:

* Środowisko jest przechowywane w `_env` polu.
* `_env`jest używany w systemie `ConfigureServices` i `Configure` do zastosowania konfiguracji uruchamiania na podstawie środowiska aplikacji.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Konwencje klasy uruchamiania

Po uruchomieniu aplikacji ASP.NET Core, [Klasa startowa](xref:fundamentals/startup) uruchamia aplikację. Aplikacja może definiować wiele `Startup` klas dla różnych środowisk. Odpowiednia `Startup` Klasa jest wybierana w czasie wykonywania. Kategoria, której sufiks nazwy jest zgodny z bieżącym środowiskiem, ma priorytet. Jeśli `Startup{EnvironmentName}` nie odnaleziono zgodnej klasy, `Startup` Klasa jest używana. Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko. Typowe aplikacje nie będą potrzebować tego podejścia.

Aby zaimplementować klasy oparte na środowisku `Startup` , należy utworzyć `Startup{EnvironmentName}` klasy i klasę Fallback `Startup` :

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

Użyj przeciążenia [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , które akceptuje nazwę zestawu:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Konwencje metody uruchamiania

[Skonfiguruj](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , aby obsługiwały wersje formularzy `Configure<EnvironmentName>` i `Configure<EnvironmentName>Services` . Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego przy użyciu zmiennej środowiskowej.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Środowiska

ASP.NET Core odczytuje zmienną środowiskową `ASPNETCORE_ENVIRONMENT` przy uruchamianiu aplikacji i zapisuje wartość w [IHostingEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`można ustawić na dowolną wartość, ale trzy wartości są dostarczane przez strukturę:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>wartooć

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Wywołuje [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` , gdy jest ustawiony na `Development` .
* Wywołuje [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , gdy wartość `ASPNETCORE_ENVIRONMENT` jest ustawiona jedną z następujących wartości:

  * `Staging`
  * `Production`
  * `Staging_2`

[Pomocnik tagu środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa wartości `IHostingEnvironment.EnvironmentName` do dołączania lub wykluczania znaczników w elemencie:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

W systemach Windows i macOS, zmienne środowiskowe i wartości nie uwzględniają wielkości liter. Zmienne środowiskowe systemu Linux i wartości domyślnie uwzględniają wielkość liter.

### <a name="development"></a>Opracowywanie zawartości

Środowisko programistyczne może włączyć funkcje, które nie powinny być ujawnione w środowisku produkcyjnym. Na przykład szablony ASP.NET Core umożliwiają [stronie wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.

Środowisko do tworzenia maszyn lokalnych można ustawić w *Properties\launchSettings.jsw* pliku projektu. Wartości środowiskowe ustawione w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.

Poniższy kod JSON przedstawia trzy profile z *launchSettings.jsw* pliku:

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
> `applicationUrl`Właściwość w *launchSettings.jsna* można określić listę adresów URL serwera. Użyj średnika między adresami URL na liście:
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

Gdy aplikacja jest uruchamiana z [uruchomieniem dotnet](/dotnet/core/tools/dotnet-run), pierwszy profil z `"commandName": "Project"` jest używany. Wartość `commandName` Określa serwer sieci Web do uruchomienia. `commandName`może być jedną z następujących czynności:

* `IISExpress`
* `IIS`
* `Project`(co spowoduje uruchomienie Kestrel)

Gdy aplikacja jest uruchamiana z [uruchomieniem dotnet](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* jest odczytywany, jeśli jest dostępny. `environmentVariables`Ustawienia w *launchSettings.jsprzy* zastępowaniu zmiennych środowiskowych.
* Zostanie wyświetlone środowisko hostingu.

Następujące dane wyjściowe pokazują aplikację uruchomioną z [uruchomieniem dotnet](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Karta **debugowanie** właściwości projektu programu Visual Studio udostępnia graficzny interfejs użytkownika umożliwiający edytowanie *launchSettings.jsw* pliku:

![Ustawienia właściwości projektu zmienne środowiskowe](environments/_static/project-properties-debug.png)

Zmiany wprowadzone w profilach projektu mogą zacząć obowiązywać dopiero po ponownym uruchomieniu serwera sieci Web. Kestrel musi zostać uruchomiona ponownie, zanim będzie mógł wykryć zmiany wprowadzone w środowisku.

> [!WARNING]
> *launchSettings.js* nie należy przechowywać wpisów tajnych. [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets) można używać do przechowywania wpisów tajnych na potrzeby lokalnego tworzenia oprogramowania.

W przypadku korzystania z [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *. programu vscode/launch.js* . Poniższy przykład ustawia środowisko na `Development` :

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

Element *. programu vscode/launch.jsw* pliku w projekcie nie jest odczytywany podczas uruchamiania aplikacji `dotnet run` w taki sam sposób, jak *Właściwości/launchSettings.jsna*. Podczas uruchamiania aplikacji w środowisku deweloperskim, która nie ma *launchSettings.jsw* pliku, należy ustawić środowisko przy użyciu zmiennej środowiskowej lub argumentu wiersza polecenia do `dotnet run` polecenia.

### <a name="production"></a>Produkcja

Środowisko produkcyjne powinno być skonfigurowane w celu zmaksymalizowania zabezpieczeń, wydajności i niezawodności aplikacji. Niektóre typowe ustawienia, które różnią się od programowania, to m.in.:

* Pamięć.
* Zasoby po stronie klienta są powiązane, zminimalizowanego i mogą być obsługiwane przez sieć CDN.
* Wyłączono strony błędów diagnostyki.
* Włączono przyjazne strony błędów.
* Włączono rejestrowanie i monitorowanie produkcji. Na przykład [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Ustawianie środowiska

Często warto ustawić określone środowisko do testowania przy użyciu zmiennej środowiskowej lub ustawienia platformy. Jeśli środowisko nie jest ustawione, domyślnie jest to `Production` , co spowoduje wyłączenie większości funkcji debugowania. Metoda ustawiania środowiska zależy od systemu operacyjnego.

Po skompilowaniu hosta ostatnie ustawienie środowiska odczytywane przez aplikację określa środowisko aplikacji. Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.

### <a name="environment-variable-or-platform-setting"></a>Zmienna środowiskowa lub ustawienie platformy

#### <a name="azure-app-service"></a>Azure App Service

Aby ustawić środowisko w [Azure App Service](https://azure.microsoft.com/services/app-service/), wykonaj następujące czynności:

1. Wybierz aplikację z bloku **App Services** .
1. W grupie **Ustawienia** wybierz blok **Konfiguracja** .
1. Na karcie **Ustawienia aplikacji** wybierz pozycję **nowe ustawienie aplikacji**.
1. W oknie **Dodawanie/Edytowanie ustawienia aplikacji** Podaj `ASPNETCORE_ENVIRONMENT` **nazwę**. W polu **wartość**Podaj środowisko (na przykład `Staging` ).
1. Zaznacz pole wyboru **ustawienie miejsca wdrożenia** , jeśli chcesz, aby ustawienie środowiska pozostawało w bieżącym gnieździe w przypadku wymiany miejsc wdrożenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.
1. Wybierz **przycisk OK** , aby zamknąć okno **Dodawanie/Edytowanie ustawienia aplikacji** .
1. Wybierz pozycję **Zapisz** w górnej części bloku **Konfiguracja** .

Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji (zmienna środowiskowa) w Azure Portal.

#### <a name="windows"></a>Windows

Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja zostanie uruchomiona przy użyciu [uruchomienia dotnet](/dotnet/core/tools/dotnet-run), są używane następujące polecenia:

**Wiersz polecenia**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**Program PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Te polecenia zaczną obowiązywać tylko dla bieżącego okna. Po zamknięciu okna `ASPNETCORE_ENVIRONMENT` ustawienie przywraca ustawienie domyślne lub wartość maszyny.

Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:

* Otwórz **Panel sterowania** > **system** > **Zaawansowane ustawienia systemowe** i Dodaj lub Edytuj `ASPNETCORE_ENVIRONMENT` wartość:

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiskowa ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Otwórz wiersz polecenia z uprawnieniami administracyjnymi i Użyj `setx` polecenia lub Otwórz wiersz polecenia administracyjnych programu PowerShell i Użyj `[Environment]::SetEnvironmentVariable` :

  **Wiersz polecenia**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M`Przełącznik wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiana dla konta użytkownika.

  **Program PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine`Wartość opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu. Jeśli wartość opcji jest zmieniana na `User` , zmienna środowiskowa jest ustawiana dla konta użytkownika.

Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona globalnie, obowiązuje `dotnet run` w każdym oknie polecenia otwartym po ustawieniu wartości.

**web.config**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową o *web.config*, zobacz sekcję *Ustawianie zmiennych środowiskowych* w temacie <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .

**Plik projektu lub profil publikacji**

**W przypadku wdrożeń usług Windows IIS:** Uwzględnij `<EnvironmentName>` Właściwość w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub w pliku projektu. To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Na pulę aplikacji IIS**

Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej przez usługi IIS 10,0 lub nowszej), zobacz sekcję *polecenieAppCmd.exe* w temacie [zmienne środowiskowe &lt; &gt; environmentVariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) . Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.

> [!IMPORTANT]
> Podczas hostowania aplikacji w usługach IIS i dodawania lub zmieniania `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej Użyj jednego z poniższych metod, aby nowa wartość była pobierana przez aplikacje:
>
> * Wykonaj `net stop was /y` , a następnie `net start w3svc` z wiersza polecenia.
> * Uruchom ponownie serwer.

#### <a name="macos"></a>macOS

Ustawienie bieżącego środowiska dla macOS można wykonać w trybie online podczas uruchamiania aplikacji:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Alternatywnie można ustawić środowisko przy użyciu programu `export` przed uruchomieniem aplikacji:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *. bashrc* lub *. bash_profile* . Edytuj plik przy użyciu dowolnego edytora tekstu. Dodaj następującą instrukcję:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

W przypadku dystrybucji systemu Linux Użyj `export` polecenia z wiersza polecenia w przypadku ustawień zmiennych opartych na sesji i pliku *bash_profile* dla ustawień środowiska na poziomie komputera.

### <a name="set-the-environment-in-code"></a>Ustawianie środowiska w kodzie

Wywołanie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> podczas kompilowania hosta. Zobacz: <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Konfiguracja według środowiska

Aby załadować konfigurację według środowiska, zalecamy:

* pliki *AppSettings* (*appSettings. { Environment}. JSON*). Zobacz: <xref:fundamentals/configuration/index#json-configuration-provider>.
* Zmienne środowiskowe (ustawiane dla każdego systemu, w którym jest hostowana aplikacja). Zobacz <xref:fundamentals/host/web-host#environment> i <xref:security/app-secrets#environment-variables> .
* Secret Manager (tylko w środowisku programistycznym). Zobacz: <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Klasy i metody uruchamiania oparte na środowisku

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Wsuń IHostingEnvironment do Startup.Configuruj

Wsuń <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure` . Takie podejście jest przydatne, gdy aplikacja wymaga konfiguracji tylko `Startup.Configure` dla kilku środowisk z minimalnymi różnicami w kodzie dla danego środowiska.

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Wsuń IHostingEnvironment do klasy startowej

Wsuń <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup` konstruktora i przypisz usługę do pola do użycia w całej `Startup` klasie. Takie podejście jest przydatne, gdy aplikacja wymaga konfiguracji uruchamiania tylko dla kilku środowisk z minimalnymi różnicami w kodzie dla danego środowiska.

W poniższym przykładzie:

* Środowisko jest przechowywane w `_env` polu.
* `_env`jest używany w systemie `ConfigureServices` i `Configure` do zastosowania konfiguracji uruchamiania na podstawie środowiska aplikacji.

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

Po uruchomieniu aplikacji ASP.NET Core, [Klasa startowa](xref:fundamentals/startup) uruchamia aplikację. Aplikacja może definiować oddzielne `Startup` klasy dla różnych środowisk (na przykład `StartupDevelopment` ). Odpowiednia `Startup` Klasa jest wybierana w czasie wykonywania. Kategoria, której sufiks nazwy jest zgodny z bieżącym środowiskiem, ma priorytet. Jeśli `Startup{EnvironmentName}` nie odnaleziono zgodnej klasy, `Startup` Klasa jest używana. Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.

Aby zaimplementować klasy oparte na środowisku `Startup` , należy utworzyć `Startup{EnvironmentName}` klasę dla każdego używanego środowiska i klasy rezerwowej `Startup` :

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

Użyj przeciążenia [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , które akceptuje nazwę zestawu:

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

[Skonfiguruj](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , aby obsługiwały wersje formularzy `Configure<EnvironmentName>` i `Configure<EnvironmentName>Services` . Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
