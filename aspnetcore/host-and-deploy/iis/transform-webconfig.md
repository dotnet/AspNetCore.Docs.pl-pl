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
# <a name="transform-webconfig"></a><span data-ttu-id="87349-103">Przekształcanie pliku web.config</span><span class="sxs-lookup"><span data-stu-id="87349-103">Transform web.config</span></span>

<span data-ttu-id="87349-104">Przez [Vijay Ramakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="87349-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="87349-105">Przekształcenia do pliku *web.config* mogą być stosowane automatycznie, gdy aplikacja jest publikowana na podstawie:</span><span class="sxs-lookup"><span data-stu-id="87349-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="87349-106">Konfiguracja kompilacji</span><span class="sxs-lookup"><span data-stu-id="87349-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="87349-107">Profil</span><span class="sxs-lookup"><span data-stu-id="87349-107">Profile</span></span>](#profile)
* [<span data-ttu-id="87349-108">Środowisko</span><span class="sxs-lookup"><span data-stu-id="87349-108">Environment</span></span>](#environment)
* [<span data-ttu-id="87349-109">Niestandardowy</span><span class="sxs-lookup"><span data-stu-id="87349-109">Custom</span></span>](#custom)

<span data-ttu-id="87349-110">Te przekształcenia występują dla jednego z następujących scenariuszy generowania *web.config:*</span><span class="sxs-lookup"><span data-stu-id="87349-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="87349-111">Generowane automatycznie przez `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="87349-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="87349-112">Dostarczone przez dewelopera w [katalogu głównym zawartości](xref:fundamentals/index#content-root) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="87349-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="87349-113">Konfiguracja kompilacji</span><span class="sxs-lookup"><span data-stu-id="87349-113">Build configuration</span></span>

<span data-ttu-id="87349-114">Transformacje konfiguracji kompilacji są uruchamiane jako pierwsze.</span><span class="sxs-lookup"><span data-stu-id="87349-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="87349-115">Dołącz *sieć web.{ CONFIGURATION}.config* file for each [build configuration (Debug| Release)](/dotnet/core/tools/dotnet-publish#options) wymagających transformacji *web.config.*</span><span class="sxs-lookup"><span data-stu-id="87349-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="87349-116">W poniższym przykładzie zmienna środowiskowa specyficzna dla konfiguracji jest ustawiona w *sieci Web. Release.config*:</span><span class="sxs-lookup"><span data-stu-id="87349-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

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

<span data-ttu-id="87349-117">Transformacja jest stosowana, gdy konfiguracja jest ustawiona na *Zwolnij:*</span><span class="sxs-lookup"><span data-stu-id="87349-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="87349-118">Właściwość MSBuild dla `$(Configuration)`konfiguracji jest .</span><span class="sxs-lookup"><span data-stu-id="87349-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="87349-119">Profil</span><span class="sxs-lookup"><span data-stu-id="87349-119">Profile</span></span>

<span data-ttu-id="87349-120">Przekształcenia profilu są uruchamiane na drugim miejscu, po przekształcenia [konfiguracji kompilacji.](#build-configuration)</span><span class="sxs-lookup"><span data-stu-id="87349-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="87349-121">Dołącz *sieć web.{ PLIK PROFILE}.config* dla każdej konfiguracji profilu wymagającej przekształcenia *web.config.*</span><span class="sxs-lookup"><span data-stu-id="87349-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="87349-122">W poniższym przykładzie zmienna środowiskowa specyficzne dla profilu jest ustawiona w *sieci Web. FolderProfile.config* dla profilu publikowania folderów:</span><span class="sxs-lookup"><span data-stu-id="87349-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

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

<span data-ttu-id="87349-123">Transformacja jest stosowana, gdy profil jest *FolderProfile:*</span><span class="sxs-lookup"><span data-stu-id="87349-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="87349-124">Właściwość MSBuild dla nazwy `$(PublishProfile)`profilu to .</span><span class="sxs-lookup"><span data-stu-id="87349-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="87349-125">Jeśli żaden profil nie zostanie przekazany, domyślną nazwą profilu jest **FileSystem** i *web. FileSystem.config* jest stosowany, jeśli plik jest obecny w katalogu głównym zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="87349-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="87349-126">Środowisko</span><span class="sxs-lookup"><span data-stu-id="87349-126">Environment</span></span>

<span data-ttu-id="87349-127">Przekształcenia środowiska są uruchamiane na trzecim miejscu, po [konfiguracji kompilacji](#build-configuration) i [przekształcenia profilu.](#profile)</span><span class="sxs-lookup"><span data-stu-id="87349-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="87349-128">Dołącz *sieć web.{ ŚRODOWISKO}.config* dla każdego [środowiska](xref:fundamentals/environments) wymagającego transformacji *web.config.*</span><span class="sxs-lookup"><span data-stu-id="87349-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="87349-129">W poniższym przykładzie zmienna środowiskowa specyficzna dla środowiska jest ustawiona w *sieci Web. Production.config* dla środowiska produkcyjnego:</span><span class="sxs-lookup"><span data-stu-id="87349-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

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

<span data-ttu-id="87349-130">Transformacja jest stosowana, gdy środowiskiem jest *Produkcja:*</span><span class="sxs-lookup"><span data-stu-id="87349-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="87349-131">Właściwość MSBuild dla `$(EnvironmentName)`środowiska jest .</span><span class="sxs-lookup"><span data-stu-id="87349-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="87349-132">Podczas publikowania w programie Visual Studio <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>i korzystania z profilu publikowania zobacz .</span><span class="sxs-lookup"><span data-stu-id="87349-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="87349-133">Zmienna środowiskowa `ASPNETCORE_ENVIRONMENT` jest automatycznie dodawana do pliku *web.config* po określeniu nazwy środowiska.</span><span class="sxs-lookup"><span data-stu-id="87349-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="87349-134">Niestandardowy</span><span class="sxs-lookup"><span data-stu-id="87349-134">Custom</span></span>

<span data-ttu-id="87349-135">Przekształcenia niestandardowe są uruchamiane jako ostatnie, po [konfiguracji kompilacji,](#build-configuration) [profilu](#profile)i [przekształcaniu środowiska.](#environment)</span><span class="sxs-lookup"><span data-stu-id="87349-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="87349-136">Dołącz plik *{CUSTOM_NAME}.transform* dla każdej konfiguracji niestandardowej wymagającej transformacji *web.config.*</span><span class="sxs-lookup"><span data-stu-id="87349-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="87349-137">W poniższym przykładzie niestandardowa zmienna środowiskowa transformacji jest ustawiona w *pliku custom.transform:*</span><span class="sxs-lookup"><span data-stu-id="87349-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

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

<span data-ttu-id="87349-138">Transformacja jest stosowana, `CustomTransformFileName` gdy właściwość jest przekazywana do polecenia [publikowania dotnet:](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="87349-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="87349-139">Właściwość MSBuild dla nazwy `$(CustomTransformFileName)`profilu to .</span><span class="sxs-lookup"><span data-stu-id="87349-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="87349-140">Zapobieganie transformacji web.config</span><span class="sxs-lookup"><span data-stu-id="87349-140">Prevent web.config transformation</span></span>

<span data-ttu-id="87349-141">Aby zapobiec przekształceniom pliku *web.config,* ustaw `$(IsWebConfigTransformDisabled)`właściwość MSBuild:</span><span class="sxs-lookup"><span data-stu-id="87349-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="87349-142">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="87349-142">Additional resources</span></span>

* <span data-ttu-id="87349-143">[Składnia transformacji web.config dla wdrażania projektu aplikacji sieci Web](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="87349-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="87349-144">[Składnia transformacji web.config dla wdrażania projektu sieci Web przy użyciu programu Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="87349-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
