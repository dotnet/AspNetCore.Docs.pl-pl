---
title: Profile publikowania w programie Visual Studio (pubxml) dla ASP.NET wdrożenia aplikacji Core
author: rick-anderson
description: Dowiedz się, jak tworzyć profile publikowania w programie Visual Studio i używać ich do zarządzania wdrożeniami aplikacji ASP.NET Core do różnych obiektów docelowych.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659377"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Profile publikowania w programie Visual Studio (pubxml) dla ASP.NET wdrożenia aplikacji Core

Przez [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) i [Rick Anderson](https://twitter.com/RickAndMSFT)

Ten dokument koncentruje się na użyciu programu Visual Studio 2019 lub nowszego do tworzenia i używania profilów publikowania. Profile publikowania utworzone za pomocą programu Visual Studio mogą być używane z MSBuild i Visual Studio. Aby uzyskać instrukcje dotyczące <xref:tutorials/publish-to-azure-webapp-using-vs>publikowania na platformie Azure, zobacz .

Polecenie `dotnet new mvc` tworzy plik projektu zawierający następujący [ \<element projektu>](/visualstudio/msbuild/project-element-msbuild)na poziomie głównym:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

Atrybut poprzedniego `<Project>` elementu importuje [właściwości](/visualstudio/msbuild/msbuild-properties) i [obiekty docelowe](/visualstudio/msbuild/msbuild-targets) MSBuild z *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* i *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets.* `Sdk` Domyślną lokalizacją `$(MSBuildSDKsPath)` (z programem Visual Studio 2019 Enterprise) jest folder *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks.*

`Microsoft.NET.Sdk.Web`(Web SDK) zależy od innych SDK, w tym `Microsoft.NET.Sdk` (.NET Core SDK) i `Microsoft.NET.Sdk.Razor` [(Razor SDK](xref:razor-pages/sdk)). Właściwości MSBuild i obiekty docelowe skojarzone z każdym zależnym sdk są importowane. Cele publikowania importują odpowiedni zestaw obiektów docelowych na podstawie użytej metody publikowania.

Gdy MSBuild lub Visual Studio ładuje projekt, występują następujące akcje wysokiego poziomu:

* Zbuduj projekt
* Oblicz pliki do opublikowania
* Publikowanie plików w miejscu docelowym

## <a name="compute-project-items"></a>Obliczanie elementów projektu

Po załadowaniu projektu obliczane są [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (pliki). Typ elementu określa sposób przetwarzania pliku. Domyślnie pliki *cs* są uwzględniane `Compile` na liście elementów. Pliki na `Compile` liście elementów są kompilowane.

Lista `Content` elementów zawiera pliki, które są publikowane oprócz wyjść kompilacji. Domyślnie pliki pasujące `wwwroot\**`do `**\*.config`wzorców `**\*.json` i są `Content` uwzględniane na liście elementów. Na przykład `wwwroot\**` [wzorzec globbingu](https://gruntjs.com/configuring-tasks#globbing-patterns) pasuje do wszystkich plików w folderze *wwwroot* i jego podfolderach.

::: moniker range=">= aspnetcore-3.0"

Web SDK importuje [SDK Razor](xref:razor-pages/sdk). W rezultacie pliki pasujące `**\*.cshtml` do `**\*.razor` wzorców i są `Content` również zawarte na liście elementów.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Web SDK importuje [SDK Razor](xref:razor-pages/sdk). W rezultacie pliki pasujące do `**\*.cshtml` wzorca `Content` są również uwzględniane na liście elementów.

::: moniker-end

Aby jawnie dodać plik do listy publikowania, dodaj go bezpośrednio do pliku *csproj,* jak pokazano w sekcji [Dołącz pliki.](#include-files)

Podczas wybierania przycisku **Publikuj** w programie Visual Studio lub podczas publikowania z wiersza polecenia:

* Właściwości/elementy są obliczane (pliki, które są potrzebne do kompilacji).
* **Tylko visual studio:** Pakiety NuGet są przywracane. (Przywracanie musi być jawne przez użytkownika w interfejsie wiersza polecenia.
* Projekt jest budowany.
* Elementy publikowania są obliczane (pliki, które są potrzebne do opublikowania).
* Projekt jest publikowany (obliczone pliki są kopiowane do miejsca docelowego publikowania).

Gdy ASP.NET core projektu odwołania `Microsoft.NET.Sdk.Web` w pliku projektu, *plik app_offline.htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci web. Gdy plik jest obecny, ASP.NET Moduł podstawowy bezpiecznie zamyka aplikację i obsługuje *plik app_offline.htm* podczas wdrażania. Aby uzyskać więcej informacji, zobacz [ASP.NET odwołanie do konfiguracji modułu rdzenia](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Podstawowe publikowanie wiersza polecenia

Publikowanie wiersza polecenia działa na wszystkich platformach obsługiwanych przez platformę .NET Core i nie wymaga programu Visual Studio. W poniższych przykładach polecenie [dotnet .NET](/dotnet/core/tools/dotnet-publish) Core CLI jest uruchamiane z katalogu projektu (który zawiera plik *csproj).* Jeśli folder projektu nie jest bieżącym katalogiem roboczym, jawnie przekaż ścieżkę pliku projektu. Przykład:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Uruchom następujące polecenia, aby utworzyć i opublikować aplikację sieci web:

```dotnetcli
dotnet new mvc
dotnet publish
```

Polecenie `dotnet publish` tworzy odmianę następujących danych wyjściowych:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Domyślnym formatem folderu publikowania jest *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*. Na przykład *bin\Debug\netcoreapp2.2\publish\\*.

Następujące polecenie określa `Release` kompilację i katalog publikowania:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Polecenie `dotnet publish` wywołuje MSBuild, który `Publish` wywołuje obiekt docelowy. Wszystkie parametry `dotnet publish` przekazywane do są przekazywane do MSBuild. I `-c` `-o` parametry mapują odpowiednio właściwości `Configuration` i `OutputPath` MSBuild.

MsBuild właściwości mogą być przekazywane przy użyciu jednego z następujących formatów:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Na przykład następujące polecenie publikuje `Release` kompilację do udziału sieciowego. Udział sieciowy jest określony z ukośnikami do przodu (*//r8/*) i działa na wszystkich obsługiwanych platformach .NET Core.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Upewnij się, że opublikowana aplikacja do wdrożenia nie jest uruchomiona. Pliki w folderze *publikowania* są blokowane, gdy aplikacja jest uruchomiona. Nie można wywdrożenia, ponieważ nie można skopiować zablokowanych plików.

## <a name="publish-profiles"></a>Profile publikowania

W tej sekcji używa programu Visual Studio 2019 lub nowszego do utworzenia profilu publikowania. Po utworzeniu profilu publikowanie w programie Visual Studio lub wiersz polecenia jest dostępne. Profile publikowania mogą uprościć proces publikowania i może istnieć dowolna liczba profili.

Utwórz profil publikowania w programie Visual Studio, wybierając jedną z następujących ścieżek:

* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Publikuj**.
* Z menu **Kompilacja** wybierz **polecenie Publikuj {PROJECT NAME}.**

Zostanie wyświetlona karta **Publikuj** na stronie Możliwości aplikacji. Jeśli projekt nie ma profilu publikowania, zostanie wyświetlona strona **Wybierz miejsce docelowe publikowania.** Zostaniesz poproszony o wybranie jednego z następujących celów publikowania:

* Azure App Service
* Usługa Azure App Service w systemie Linux
* Azure Virtual Machines
* Folder
* Usługi IIS, FTP, Web Deploy (dla dowolnego serwera sieci Web)
* Profil importu

Aby określić najbardziej odpowiedni cel publikowania, zobacz [Jakie opcje publikowania są dla mnie odpowiednie.](/visualstudio/ide/not-in-toc/web-publish-options)

Po wybraniu obiektu docelowego publikowania **folderu** należy określić ścieżkę folderu do przechowywania opublikowanych zasobów. Domyślną ścieżką folderu jest *bin\\{PROJECT CONFIGURATION}\\\\{TARGET FRAMEWORK MONIKER}\publish*. Na przykład *bin\Release\netcoreapp2.2\publish\\*. Wybierz przycisk **Utwórz profil,** aby zakończyć.

Po utworzeniu profilu publikowania zawartość karty **Publikowanie** zostanie ulegna zmianie. Nowo utworzony profil pojawi się na liście rozwijanej. Poniżej listy rozwijanej wybierz pozycję **Utwórz nowy profil,** aby utworzyć inny nowy profil.

Narzędzie publikowania w programie Visual Studio tworzy plik *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild opisujący profil publikowania. Plik *.pubxml:*

* Zawiera ustawienia konfiguracji publikowania i jest zużywany przez proces publikowania.
* Można zmodyfikować, aby dostosować proces kompilacji i publikowania.

Podczas publikowania w witrynie docelowej platformy Azure plik *.pubxml* zawiera identyfikator subskrypcji platformy Azure. W tym typie docelowym odradza się dodawanie tego pliku do kontroli źródła. Podczas publikowania w celu docelowym spoza platformy Azure można bezpiecznie zaewidencjonować plik *pubxml.*

Poufne informacje (takie jak hasło publikowania) są szyfrowane na poziomie użytkownika/komputera. Jest on przechowywany w pliku *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user.* Ponieważ ten plik może przechowywać poufne informacje, nie należy go zaewidencjonować do kontroli źródła.

Aby zapoznać się z omówieniem publikowania aplikacji <xref:host-and-deploy/index>sieci web ASP.NET Core, zobacz . Zadania i cele MSBuild niezbędne do opublikowania ASP.NET core aplikacji sieci web są open-source w [repozytorium aspnet/websdk](https://github.com/aspnet/websdk).

Następujące polecenia mogą używać profilów publikowania folderów, MSDeploy i [Kudu.](https://github.com/projectkudu/kudu/wiki) Ponieważ MSDeploy nie obsługuje między platformami, następujące opcje MSDeploy są obsługiwane tylko w systemie Windows.

**Folder (działa między platformami):**

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**Pakiet MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

W poprzednich przykładach:

* `dotnet publish`i `dotnet build` obsługuje interfejsy API Kudu do publikowania na platformie Azure z dowolnej platformy. Visual Studio publikowania obsługuje interfejsy API Kudu, ale jest obsługiwany przez WebSDK dla publikowania między platformami na platformie Azure.
* Nie przekazuj `DeployOnBuild` `dotnet publish` do polecenia.

Aby uzyskać więcej informacji, zobacz [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

Dodaj profil publikowania do folderu *Właściwości/Publikowanie profilu* projektu z następującą zawartością:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Przykład publikowania folderów

Podczas publikowania z profilem o nazwie *FolderProfile*użyj jednego z następujących poleceń:

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

[Dotnet kompilacji polecenia .NET](/dotnet/core/tools/dotnet-build) Core `msbuild` CLI wywołuje wywołania polecenia kompilacji i publikowania. Polecenia `dotnet build` `msbuild` i są równoważne podczas przekazywania w profilu folderu. Podczas `msbuild` wywoływania bezpośrednio w systemie Windows używana jest wersja programu .NET Framework programu MSBuild. Wywoływanie `dotnet build` profilu bez folderu:

* Wywołuje `msbuild`, który używa MSDeploy.
* Powoduje błąd (nawet w przypadku pracy w systemie Windows). Aby opublikować z profilem `msbuild` spoza folderu, zadzwoń bezpośrednio.

Następujący profil publikowania folderów został utworzony za pomocą programu Visual Studio i publikuje w udziale sieciowym:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

W poprzednim przykładzie:

* Właściwość `<ExcludeApp_Data>` jest obecny tylko w celu spełnienia wymagań schematu XML. Właściwość `<ExcludeApp_Data>` nie ma wpływu na proces publikowania, nawet jeśli w katalogu głównym projektu znajduje się folder *App_Data.* Folder *App_Data* nie jest przedmiotem specjalnego traktowania, tak jak w projektach ASP.NET 4.x.

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* Właściwość `<LastUsedBuildConfiguration>` jest `Release`ustawiona na . Podczas publikowania z programu Visual `<LastUsedBuildConfiguration>` Studio wartość jest ustawiana przy użyciu wartości po uruchomieniu procesu publikowania. `<LastUsedBuildConfiguration>`jest specjalny i nie powinien być zastępowany w importowanym pliku MSBuild. Ta właściwość może jednak zostać zastąpiona z wiersza polecenia przy użyciu jednego z następujących metod.
  * Korzystanie z interfejsu wiersza polecenia .NET Core:

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * Korzystanie z usługi MSBuild:

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Aby uzyskać więcej informacji, zobacz [MSBuild: jak ustawić właściwość konfiguracji](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publikowanie w punkcie końcowym MSDeploy z wiersza polecenia

W poniższym przykładzie użyto aplikacji sieci web ASP.NET Core utworzonej przez program Visual Studio o nazwie *AzureWebApp*. Profil publikowania usługi Azure Apps jest dodawany z programem Visual Studio. Aby uzyskać więcej informacji na temat tworzenia profilu, zobacz sekcję [Publikowanie profili.](#publish-profiles)

Aby wdrożyć aplikację przy użyciu `msbuild` profilu publikowania, należy wykonać polecenie z **wiersza polecenia dewelopera**programu Visual Studio . Wiersz polecenia jest dostępny w folderze *programu Visual Studio* menu **Start** na pasku zadań systemu Windows. Aby uzyskać łatwiejszy dostęp, można dodać wiersz polecenia do menu **Narzędzia** w programie Visual Studio. Aby uzyskać więcej informacji, zobacz [Wiersz polecenia dewelopera dla programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild używa następującej składni polecenia:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {ŚCIEŻKA} &ndash; Ścieżka do pliku projektu aplikacji.
* {PROFIL} &ndash; Nazwa profilu publikowania.
* {NAZWA UŻYTKOWNIKA} &ndash; Nazwa użytkownika MSDeploy. {NAZWA UŻYTKOWNIKA} można znaleźć w profilu publikowania.
* {HASŁO} &ndash; MSDeploy hasło. Uzyskaj {HASŁO} z *{PROFILE}. PublishSettings* plik. Pobierz plik *. PublishSettings* plik z jednego z:
  * **Eksplorator rozwiązań:** wybierz **Wyświetl** > **Eksploratora chmury**. Połącz się z subskrypcją platformy Azure. Otwórz **usługi aplikacji**. Kliknij prawym przyciskiem myszy aplikację. Wybierz **pozycję Pobierz profil publikowania**.
  * Portal Azure: Wybierz **pobierz profil publikowania** w panelu **Przegląd** aplikacji sieci web.

W poniższym przykładzie użyto profilu publikowania o nazwie *AzureWebApp — Wdrażanie w sieci Web:*

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Profil publikowania może być również używany z [poleceniem dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) .NET Core z powłoki poleceń systemu Windows:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> Polecenie `dotnet msbuild` jest poleceniem wieloplatformowym i może kompilować aplikacje ASP.NET Core w systemach macOS i Linux. Jednak MSBuild w systemach macOS i Linux nie jest w stanie wdrożyć aplikację na platformie Azure lub innych punktów końcowych MSDeploy.

## <a name="set-the-environment"></a>Ustawianie środowiska

Dołącz `<EnvironmentName>` właściwość do profilu publikowania (*pubxml*) lub pliku projektu, aby ustawić [środowisko](xref:fundamentals/environments)aplikacji:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Jeśli wymagane są przekształcenia *web.config* (na przykład ustawienie zmiennych środowiskowych na <xref:host-and-deploy/iis/transform-webconfig>podstawie konfiguracji, profilu lub środowiska), zobacz .

## <a name="exclude-files"></a>Wykluczanie plików

Podczas publikowania aplikacji sieci Web ASP.NET Core uwzględniane są następujące zasoby:

* Buduj artefakty
* Foldery i pliki pasujące do następujących wzorców globbingu:
  * `**\*.config`(na przykład *web.config*)
  * `**\*.json`(na przykład *appsettings.json*)
  * `wwwroot\**`

MSBuild obsługuje [wzorce globbingu](https://gruntjs.com/configuring-tasks#globbing-patterns). Na przykład następujący `<Content>` element pomija kopiowanie plików tekstowych (*txt*) w folderze *wwwroot\content* i jego podfolderach:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Poprzednie znaczniki można dodać do profilu publikowania lub pliku *csproj.* Po dodaniu do pliku *csproj* reguła jest dodawana do wszystkich profili publikowania w projekcie.

Następujący `<MsDeploySkipRules>` element wyklucza wszystkie pliki z folderu *wwwroot\content:*

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`nie spowoduje usunięcia obiektów docelowych *pomijania* z lokacji wdrażania. `<Content>`pliki i foldery docelowe są usuwane z witryny wdrażania. Załóżmy na przykład, że wdrożona aplikacja internetowa miała następujące pliki:

* *Widoki/Strona główna/Informacje1.cshtml*
* *Widoki/Strona główna/About2.cshtml*
* *Widoki/Strona główna/About3.cshtml*

Jeśli zostaną `<MsDeploySkipRules>` dodane następujące elementy, te pliki nie zostaną usunięte w lokacji wdrażania.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Powyższe `<MsDeploySkipRules>` elementy uniemożliwiają *wdrażanie pominiętych* plików. Nie spowoduje to usunięcia tych plików po ich wdrożeniu.

Następujący `<Content>` element usuwa pliki docelowe w lokacji wdrażania:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Za pomocą wdrożenia wiersza `<Content>` polecenia z poprzednim elementem daje odmianę następujących danych wyjściowych:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Pliki dołączane

W poniższych sekcjach opisano różne podejścia do dołączania plików w czasie publikowania. [Sekcja Ogólne dołączanie](#general-file-inclusion) plików `DotNetPublishFiles` używa elementu, który jest dostarczany przez plik docelowy publikowania w web SDK. Sekcja [Selektywne dołączanie](#selective-file-inclusion) `ResolvedFileToPublish` plików używa elementu, który jest dostarczany przez plik docelowy publikowania w pliku .NET Core SDK. Ponieważ składnik SDK sieci Web zależy od sdk .NET Core, każdy element może być używany w projekcie ASP.NET Core.

### <a name="general-file-inclusion"></a>Ogólne dołączanie plików

Poniższy przykładowy `<ItemGroup>` element pokazuje kopiowanie folderu znajdującego się poza katalogiem projektu do folderu opublikowanej witryny. Wszystkie pliki dodane do następujących `<ItemGroup>` znaczników są domyślnie uwzględniane.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Powyższe znaczniki:

* Można dodać do pliku *csproj* lub profilu publikowania. Jeśli zostanie dodany do pliku *csproj,* zostanie uwzględniony w każdym profilu publikowania w projekcie.
* Deklaruje `_CustomFiles` element do przechowywania plików `Include` pasujących do wzorca globbing atrybutu. Folder *obrazów,* do którego odwołuje się wzorzec, znajduje się poza katalogiem projektu. [Właściwość zastrzeżona](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), o nazwie, `$(MSBuildProjectDirectory)`jest rozpoznawana na ścieżce bezwzględnej pliku projektu.
* Zawiera listę plików do `DotNetPublishFiles` elementu. Domyślnie `<DestinationRelativePath>` element elementu jest pusty. Wartość domyślna jest zastępowana w znacznikach i używa dobrze znanych `%(RecursiveDir)` [metadanych elementu,](/visualstudio/msbuild/msbuild-well-known-item-metadata) takich jak . Tekst wewnętrzny reprezentuje folder *wwwroot/images* opublikowanej witryny.

### <a name="selective-file-inclusion"></a>Selektywne dołączanie plików

Wyróżnione znaczniki w poniższym przykładzie pokazuje:

* Kopiowanie pliku znajdującego się poza projektem do folderu *wwwroot* opublikowanej witryny. Nazwa pliku *ReadMe2.md* jest zachowywana.
* Z wyłączeniem folderu *wwwroot\Content.*
* Z wyłączeniem *widoków\Strona główna\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

W poprzednim przykładzie `ResolvedFileToPublish` użyto elementu, którego domyślnym zachowaniem `Include` jest zawsze kopiowanie plików podanych w atrybucie do opublikowanej witryny. Zastądnie domyślne `<CopyToPublishDirectory>` zachowanie, dołączając element `Never` podrzędny z tekstem wewnętrznym jednego lub . `PreserveNewest` Przykład:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Aby uzyskać więcej przykładów wdrożenia, zobacz [Readme repozytorium sdk sieci Web](https://github.com/aspnet/websdk).

## <a name="run-a-target-before-or-after-publishing"></a>Uruchamianie obiektu docelowego przed lub po opublikowaniu

Wbudowane `BeforePublish` i `AfterPublish` obiekty docelowe wykonać obiekt docelowy przed lub po docelowej publikowania. Dodaj następujące elementy do profilu publikowania, aby rejestrować komunikaty konsoli zarówno przed, jak i po opublikowaniu:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publikowanie na serwerze przy użyciu niezaufanego certyfikatu

Dodaj `<AllowUntrustedCertificate>` właściwość o `True` wartości do profilu publikowania:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Usługa Kudu

Aby wyświetlić pliki we wdrożeniu aplikacji sieci Web usługi Azure App Service, użyj [usługi Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Dołącz `scm` token do nazwy aplikacji sieci web. Przykład:

| Adres URL                                    | Wynik       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Aplikacja internetowa      |
| `http://mysite.scm.azurewebsites.net/` | Usługa Kudu |

Wybierz element menu [Konsoli debugowania,](https://github.com/projectkudu/kudu/wiki/Kudu-console) aby wyświetlić, edytować, usunąć lub dodać pliki.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wdrażanie sieci Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) upraszcza wdrażanie aplikacji sieci Web i witryn sieci Web na serwerach usług IIS.
* [Repozytorium GitHub web SDK](https://github.com/aspnet/websdk/issues): Problemy z plikami i funkcje żądania do wdrożenia.
* [Publikowanie ASP.NET aplikacji sieci Web na maszynie Wirtualnej platformy Azure z programu Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
