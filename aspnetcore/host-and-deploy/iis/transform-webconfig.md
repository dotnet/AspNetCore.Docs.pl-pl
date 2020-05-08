---
title: Przekształcanie pliku web.config
author: rick-anderson
description: Dowiedz się, jak przekształcić plik Web. config podczas publikowania aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: f7e1fb0adc669b4bffa02e6688231c8f1447bd98
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775937"
---
# <a name="transform-webconfig"></a><span data-ttu-id="49ce2-103">Przekształcanie pliku web.config</span><span class="sxs-lookup"><span data-stu-id="49ce2-103">Transform web.config</span></span>

<span data-ttu-id="49ce2-104">Autor [Vijay Ramakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="49ce2-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="49ce2-105">Przekształcenia do pliku *Web. config* można zastosować automatycznie po opublikowaniu aplikacji na podstawie:</span><span class="sxs-lookup"><span data-stu-id="49ce2-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="49ce2-106">Konfiguracja kompilacji</span><span class="sxs-lookup"><span data-stu-id="49ce2-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="49ce2-107">Profil</span><span class="sxs-lookup"><span data-stu-id="49ce2-107">Profile</span></span>](#profile)
* [<span data-ttu-id="49ce2-108">Środowisko</span><span class="sxs-lookup"><span data-stu-id="49ce2-108">Environment</span></span>](#environment)
* [<span data-ttu-id="49ce2-109">Niestandardowy</span><span class="sxs-lookup"><span data-stu-id="49ce2-109">Custom</span></span>](#custom)

<span data-ttu-id="49ce2-110">Te przekształcenia występują dla jednego z następujących scenariuszy generacji *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="49ce2-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="49ce2-111">Generowane automatycznie przez `Microsoft.NET.Sdk.Web` zestaw SDK.</span><span class="sxs-lookup"><span data-stu-id="49ce2-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="49ce2-112">Udostępnione przez dewelopera w [katalogu głównym zawartości](xref:fundamentals/index#content-root) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49ce2-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="49ce2-113">Konfiguracja kompilacji</span><span class="sxs-lookup"><span data-stu-id="49ce2-113">Build configuration</span></span>

<span data-ttu-id="49ce2-114">Przekształcenia konfiguracji kompilacji są uruchamiane jako pierwsze.</span><span class="sxs-lookup"><span data-stu-id="49ce2-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="49ce2-115">Uwzględnij *Sieć Web. { Konfiguracja}* plik konfiguracyjny dla każdej [konfiguracji kompilacji (Debuguj | Wersja)](/dotnet/core/tools/dotnet-publish#options) wymagająca przekształcenia *pliku Web. config* .</span><span class="sxs-lookup"><span data-stu-id="49ce2-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="49ce2-116">W poniższym przykładzie zmienna środowiskowa specyficzna dla konfiguracji została ustawiona w *sieci Web. Release. config*:</span><span class="sxs-lookup"><span data-stu-id="49ce2-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

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

<span data-ttu-id="49ce2-117">Przekształcenie jest stosowane, gdy konfiguracja jest ustawiona na *Release*:</span><span class="sxs-lookup"><span data-stu-id="49ce2-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="49ce2-118">Właściwość programu MSBuild dla konfiguracji ma `$(Configuration)`wartość.</span><span class="sxs-lookup"><span data-stu-id="49ce2-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="49ce2-119">Profil</span><span class="sxs-lookup"><span data-stu-id="49ce2-119">Profile</span></span>

<span data-ttu-id="49ce2-120">Przekształcenia profilu są uruchamiane po drugiej, po przeprowadzeniu [konfiguracji kompilacji](#build-configuration) .</span><span class="sxs-lookup"><span data-stu-id="49ce2-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="49ce2-121">Uwzględnij *Sieć Web. { PROFIL}. config* dla każdej konfiguracji profilu wymagającej przekształcenia pliku *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="49ce2-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="49ce2-122">W poniższym przykładzie zmienna środowiskowa specyficzna dla profilu jest ustawiana w *sieci Web. FolderProfile. config* dla folderu Publikuj profil:</span><span class="sxs-lookup"><span data-stu-id="49ce2-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

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

<span data-ttu-id="49ce2-123">Przekształcenie jest stosowane, gdy profil jest *FolderProfile*:</span><span class="sxs-lookup"><span data-stu-id="49ce2-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="49ce2-124">Właściwość programu MSBuild dla nazwy profilu to `$(PublishProfile)`.</span><span class="sxs-lookup"><span data-stu-id="49ce2-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="49ce2-125">Jeśli profil nie zostanie przekazywać, domyślną nazwą profilu jest **system plików** i *Sieć Web. Plik FileSystem. config* jest stosowany, jeśli jest obecny w katalogu głównym zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49ce2-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="49ce2-126">Środowisko</span><span class="sxs-lookup"><span data-stu-id="49ce2-126">Environment</span></span>

<span data-ttu-id="49ce2-127">Przekształcenia środowiska są uruchamiane trzecią po zakończeniu [konfiguracji kompilacji](#build-configuration) i przekształceń [profilu](#profile) .</span><span class="sxs-lookup"><span data-stu-id="49ce2-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="49ce2-128">Uwzględnij *Sieć Web. { ŚRODOWISKO} plik konfiguracyjny* dla każdego [środowiska](xref:fundamentals/environments) wymagającego przekształcenia pliku *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="49ce2-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="49ce2-129">W poniższym przykładzie zmienna środowiskowa specyficzna dla środowiska jest ustawiona w *sieci Web. Production. config* dla środowiska produkcyjnego:</span><span class="sxs-lookup"><span data-stu-id="49ce2-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

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

<span data-ttu-id="49ce2-130">Transformacja jest stosowana, gdy środowisko jest *produkcyjne*:</span><span class="sxs-lookup"><span data-stu-id="49ce2-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="49ce2-131">Właściwość programu MSBuild dla środowiska to `$(EnvironmentName)`.</span><span class="sxs-lookup"><span data-stu-id="49ce2-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="49ce2-132">Przy publikowaniu z programu Visual Studio i przy użyciu profilu publikowania <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="49ce2-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="49ce2-133">Zmienna `ASPNETCORE_ENVIRONMENT` środowiskowa jest automatycznie dodawana do pliku *Web. config* po określeniu nazwy środowiska.</span><span class="sxs-lookup"><span data-stu-id="49ce2-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="49ce2-134">Niestandardowy</span><span class="sxs-lookup"><span data-stu-id="49ce2-134">Custom</span></span>

<span data-ttu-id="49ce2-135">Niestandardowe przekształcenia są uruchamiane jako ostatnie, po przeprowadzeniu [konfiguracji kompilacji](#build-configuration), [profilu](#profile)i [środowiska](#environment) .</span><span class="sxs-lookup"><span data-stu-id="49ce2-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="49ce2-136">Uwzględnij plik *{CUSTOM_NAME}. Transform* dla każdej konfiguracji niestandardowej wymagającej przekształcenia pliku *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="49ce2-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="49ce2-137">W poniższym przykładzie zmienna środowiskowa transformacji niestandardowej jest ustawiana w *Custom. Transform*:</span><span class="sxs-lookup"><span data-stu-id="49ce2-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

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

<span data-ttu-id="49ce2-138">Transformacja jest stosowana, gdy `CustomTransformFileName` właściwość jest przenoszona do [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia:</span><span class="sxs-lookup"><span data-stu-id="49ce2-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="49ce2-139">Właściwość programu MSBuild dla nazwy profilu to `$(CustomTransformFileName)`.</span><span class="sxs-lookup"><span data-stu-id="49ce2-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="49ce2-140">Zablokuj transformację pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="49ce2-140">Prevent web.config transformation</span></span>

<span data-ttu-id="49ce2-141">Aby zapobiec przekształceń pliku *Web. config* , ustaw właściwość `$(IsWebConfigTransformDisabled)`MSBuild:</span><span class="sxs-lookup"><span data-stu-id="49ce2-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="49ce2-142">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="49ce2-142">Additional resources</span></span>

* <span data-ttu-id="49ce2-143">[Składnia transformacji Web. config dla wdrożenia projektu aplikacji sieci Web](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="49ce2-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="49ce2-144">[Składnia transformacji Web. config dla wdrożenia projektu sieci Web przy użyciu programu Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="49ce2-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
