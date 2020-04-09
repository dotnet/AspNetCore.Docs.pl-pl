---
title: Przekształcanie pliku web.config
author: rick-anderson
description: Dowiedz się, jak przekształcić plik web.config podczas publikowania aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 069b9bb516644a1a722235b33d4916460488ebf2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657935"
---
# <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Przez [Vijay Ramakrishnan](https://github.com/vijayrkn)

Przekształcenia do pliku *web.config* mogą być stosowane automatycznie, gdy aplikacja jest publikowana na podstawie:

* [Konfiguracja kompilacji](#build-configuration)
* [Profil](#profile)
* [Środowisko](#environment)
* [Niestandardowy](#custom)

Te przekształcenia występują dla jednego z następujących scenariuszy generowania *web.config:*

* Generowane automatycznie przez `Microsoft.NET.Sdk.Web` SDK.
* Dostarczone przez dewelopera w [katalogu głównym zawartości](xref:fundamentals/index#content-root) aplikacji.

## <a name="build-configuration"></a>Konfiguracja kompilacji

Transformacje konfiguracji kompilacji są uruchamiane jako pierwsze.

Dołącz *sieć web.{ CONFIGURATION}.config* file for each [build configuration (Debug| Release)](/dotnet/core/tools/dotnet-publish#options) wymagających transformacji *web.config.*

W poniższym przykładzie zmienna środowiskowa specyficzna dla konfiguracji jest ustawiona w *sieci Web. Release.config*:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Transformacja jest stosowana, gdy konfiguracja jest ustawiona na *Zwolnij:*

```dotnetcli
dotnet publish --configuration Release
```

Właściwość MSBuild dla `$(Configuration)`konfiguracji jest .

## <a name="profile"></a>Profil

Przekształcenia profilu są uruchamiane na drugim miejscu, po przekształcenia [konfiguracji kompilacji.](#build-configuration)

Dołącz *sieć web.{ PLIK PROFILE}.config* dla każdej konfiguracji profilu wymagającej przekształcenia *web.config.*

W poniższym przykładzie zmienna środowiskowa specyficzne dla profilu jest ustawiona w *sieci Web. FolderProfile.config* dla profilu publikowania folderów:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Transformacja jest stosowana, gdy profil jest *FolderProfile:*

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Właściwość MSBuild dla nazwy `$(PublishProfile)`profilu to .

Jeśli żaden profil nie zostanie przekazany, domyślną nazwą profilu jest **FileSystem** i *web. FileSystem.config* jest stosowany, jeśli plik jest obecny w katalogu głównym zawartości aplikacji.

## <a name="environment"></a>Środowisko

Przekształcenia środowiska są uruchamiane na trzecim miejscu, po [konfiguracji kompilacji](#build-configuration) i [przekształcenia profilu.](#profile)

Dołącz *sieć web.{ ŚRODOWISKO}.config* dla każdego [środowiska](xref:fundamentals/environments) wymagającego transformacji *web.config.*

W poniższym przykładzie zmienna środowiskowa specyficzna dla środowiska jest ustawiona w *sieci Web. Production.config* dla środowiska produkcyjnego:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Transformacja jest stosowana, gdy środowiskiem jest *Produkcja:*

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Właściwość MSBuild dla `$(EnvironmentName)`środowiska jest .

Podczas publikowania w programie Visual Studio <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>i korzystania z profilu publikowania zobacz .

Zmienna środowiskowa `ASPNETCORE_ENVIRONMENT` jest automatycznie dodawana do pliku *web.config* po określeniu nazwy środowiska.

## <a name="custom"></a>Niestandardowy

Przekształcenia niestandardowe są uruchamiane jako ostatnie, po [konfiguracji kompilacji,](#build-configuration) [profilu](#profile)i [przekształcaniu środowiska.](#environment)

Dołącz plik *{CUSTOM_NAME}.transform* dla każdej konfiguracji niestandardowej wymagającej transformacji *web.config.*

W poniższym przykładzie niestandardowa zmienna środowiskowa transformacji jest ustawiona w *pliku custom.transform:*

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Transformacja jest stosowana, `CustomTransformFileName` gdy właściwość jest przekazywana do polecenia [publikowania dotnet:](/dotnet/core/tools/dotnet-publish)

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Właściwość MSBuild dla nazwy `$(CustomTransformFileName)`profilu to .

## <a name="prevent-webconfig-transformation"></a>Zapobieganie transformacji web.config

Aby zapobiec przekształceniom pliku *web.config,* ustaw `$(IsWebConfigTransformDisabled)`właściwość MSBuild:

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Składnia transformacji web.config dla wdrażania projektu aplikacji sieci Web](/previous-versions/dd465326(v=vs.100))
* [Składnia transformacji web.config dla wdrażania projektu sieci Web przy użyciu programu Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))
