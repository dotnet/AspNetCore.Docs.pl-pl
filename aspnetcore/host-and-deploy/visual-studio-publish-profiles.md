---
title: Profile publikacji programu Visual Studio (. pubxml) dla wdrożenia aplikacji ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć profile publikowania w programie Visual Studio i używać ich do zarządzania wdrożeniami aplikacji ASP.NET Core w różnych celach.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 1c4b1def75a76b8392427fd6916a7b5927737cac
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015455"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="065eb-103">Profile publikacji programu Visual Studio (. pubxml) dla wdrożenia aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="065eb-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="065eb-104">Autorzy [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="065eb-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="065eb-105">Ten dokument koncentruje się na używaniu programu Visual Studio 2019 lub nowszego do tworzenia profilów publikowania i używania ich.</span><span class="sxs-lookup"><span data-stu-id="065eb-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="065eb-106">Profile publikowania utworzone za pomocą programu Visual Studio mogą być używane z użyciem programu MSBuild i programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="065eb-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="065eb-107">Instrukcje dotyczące publikowania na platformie Azure znajdują się w temacie <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="065eb-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="065eb-108">`dotnet new mvc`Polecenie tworzy plik projektu zawierający następujący [ \<Project> element](/visualstudio/msbuild/project-element-msbuild)poziomu głównego:</span><span class="sxs-lookup"><span data-stu-id="065eb-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="065eb-109">Atrybut poprzedzającego `<Project>` elementu `Sdk` importuje odpowiednio [Właściwości](/visualstudio/msbuild/msbuild-properties) i [elementy docelowe](/visualstudio/msbuild/msbuild-targets) programu MSBuild z *$ (MSBuildSDKsPath) \Microsoft.NET.Sdk.Web\Sdk\Sdk.props* i *$ (MSBuildSDKsPath) \Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*.</span><span class="sxs-lookup"><span data-stu-id="065eb-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="065eb-110">Domyślną lokalizacją programu `$(MSBuildSDKsPath)` (z programem Visual Studio 2019 Enterprise) jest folder *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* .</span><span class="sxs-lookup"><span data-stu-id="065eb-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="065eb-111">`Microsoft.NET.Sdk.Web`([Zestaw SDK dla sieci Web](xref:razor-pages/web-sdk)) zależy od innych zestawów SDK, w tym `Microsoft.NET.Sdk` ([zestaw .NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) i `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="065eb-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="065eb-112">Zaimportowano właściwości i obiekty docelowe programu MSBuild skojarzone z poszczególnymi zależnymi zestawem SDK.</span><span class="sxs-lookup"><span data-stu-id="065eb-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="065eb-113">Opublikuj obiekty docelowe zaimportuj odpowiedni zbiór obiektów docelowych na podstawie użytej metody publikacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="065eb-114">Gdy program MSBuild lub program Visual Studio ładuje projekt, wykonywane są następujące akcje wysokiego poziomu:</span><span class="sxs-lookup"><span data-stu-id="065eb-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="065eb-115">Kompiluj projekt</span><span class="sxs-lookup"><span data-stu-id="065eb-115">Build project</span></span>
* <span data-ttu-id="065eb-116">Pliki obliczeniowe do opublikowania</span><span class="sxs-lookup"><span data-stu-id="065eb-116">Compute files to publish</span></span>
* <span data-ttu-id="065eb-117">Publikowanie plików w lokalizacji docelowej</span><span class="sxs-lookup"><span data-stu-id="065eb-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="065eb-118">Obliczenia elementów projektu</span><span class="sxs-lookup"><span data-stu-id="065eb-118">Compute project items</span></span>

<span data-ttu-id="065eb-119">Po załadowaniu projektu są obliczane [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (pliki).</span><span class="sxs-lookup"><span data-stu-id="065eb-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="065eb-120">Typ elementu określa sposób przetwarzania pliku.</span><span class="sxs-lookup"><span data-stu-id="065eb-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="065eb-121">Domyślnie pliki *CS* znajdują się na `Compile` liście elementów.</span><span class="sxs-lookup"><span data-stu-id="065eb-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="065eb-122">Pliki na `Compile` liście elementów są kompilowane.</span><span class="sxs-lookup"><span data-stu-id="065eb-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="065eb-123">`Content`Lista elementów zawiera pliki, które są publikowane w uzupełnieniu do danych wyjściowych kompilacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="065eb-124">Domyślnie pliki zgodne ze wzorcami `wwwroot\**` , `**\*.config` i `**\*.json` znajdują się na `Content` liście elementów.</span><span class="sxs-lookup"><span data-stu-id="065eb-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="065eb-125">Na przykład `wwwroot\**` [wzorzec obsługi symboli wieloznacznych](https://gruntjs.com/configuring-tasks#globbing-patterns) dopasowuje wszystkie pliki w folderze *wwwroot* i jego podfolderach.</span><span class="sxs-lookup"><span data-stu-id="065eb-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="065eb-126">[Zestaw SDK sieci Web](xref:razor-pages/web-sdk) importuje [ Razor zestaw SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="065eb-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="065eb-127">W związku z tym pliki zgodne ze wzorcami `**\*.cshtml` i `**\*.razor` również znajdują się na `Content` liście elementów.</span><span class="sxs-lookup"><span data-stu-id="065eb-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="065eb-128">[Zestaw SDK sieci Web](xref:razor-pages/web-sdk) importuje [ Razor zestaw SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="065eb-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="065eb-129">W efekcie pliki zgodne z `**\*.cshtml` wzorcem są również uwzględnione na `Content` liście elementów.</span><span class="sxs-lookup"><span data-stu-id="065eb-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="065eb-130">Aby jawnie dodać plik do listy publikowania, Dodaj plik bezpośrednio w pliku *. csproj* , jak pokazano w sekcji [Dołączanie plików](#include-files) .</span><span class="sxs-lookup"><span data-stu-id="065eb-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="065eb-131">Po wybraniu przycisku **Publikuj** w programie Visual Studio lub opublikowaniu z wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="065eb-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="065eb-132">Obliczane są właściwości/elementy (pliki, które są konieczne do skompilowania).</span><span class="sxs-lookup"><span data-stu-id="065eb-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="065eb-133">**Tylko Visual Studio**: pakiety NuGet są przywracane.</span><span class="sxs-lookup"><span data-stu-id="065eb-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="065eb-134">(Przywracanie musi być jawne przez użytkownika w interfejsie wiersza polecenia).</span><span class="sxs-lookup"><span data-stu-id="065eb-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="065eb-135">Projekt kompiluje.</span><span class="sxs-lookup"><span data-stu-id="065eb-135">The project builds.</span></span>
* <span data-ttu-id="065eb-136">Elementy publikowania są obliczane (pliki, które są konieczne do opublikowania).</span><span class="sxs-lookup"><span data-stu-id="065eb-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="065eb-137">Projekt jest publikowany (pliki obliczane są kopiowane do lokalizacji docelowej publikowania).</span><span class="sxs-lookup"><span data-stu-id="065eb-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="065eb-138">Gdy projekt ASP.NET Core odwołuje się do `Microsoft.NET.Sdk.Web` pliku projektu, plik *app_offline.htm* zostanie umieszczony w katalogu głównym katalogu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="065eb-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="065eb-139">Gdy plik jest obecny, moduł ASP.NET Core bezpiecznie zamyka aplikację i obsługuje plik *app_offline.htm* podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="065eb-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="065eb-140">Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="065eb-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="065eb-141">Podstawowe publikowanie w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="065eb-141">Basic command-line publishing</span></span>

<span data-ttu-id="065eb-142">Publikowanie w wierszu polecenia działa na wszystkich platformach obsługiwanych przez platformę .NET Core i nie wymaga programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="065eb-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="065eb-143">W poniższych przykładach polecenie interfejs wiersza polecenia platformy .NET Core [dotnet Publish](/dotnet/core/tools/dotnet-publish) jest uruchamiane z katalogu projektu (który zawiera plik *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="065eb-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="065eb-144">Jeśli folder projektu nie jest bieżącym katalogiem roboczym, jawnie Przekaż ścieżkę do pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="065eb-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="065eb-145">Przykład:</span><span class="sxs-lookup"><span data-stu-id="065eb-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="065eb-146">Uruchom następujące polecenia, aby utworzyć i opublikować aplikację sieci Web:</span><span class="sxs-lookup"><span data-stu-id="065eb-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="065eb-147">`dotnet publish`Polecenie tworzy odmianę następujących danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="065eb-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="065eb-148">Domyślny format folderu publikowania to \*bin\Debug \\ {Target Framework MONIKER} \publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="065eb-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="065eb-149">Na przykład \*bin\Debug\netcoreapp2.2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="065eb-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="065eb-150">Następujące polecenie określa `Release` kompilację i katalog publikowania:</span><span class="sxs-lookup"><span data-stu-id="065eb-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="065eb-151">`dotnet publish`Polecenie wywołuje program MSBuild, który wywołuje `Publish` element docelowy.</span><span class="sxs-lookup"><span data-stu-id="065eb-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="065eb-152">Wszystkie parametry przesłane do `dotnet publish` są przesyłane do programu MSBuild.</span><span class="sxs-lookup"><span data-stu-id="065eb-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="065eb-153">`-c`Parametry i są `-o` mapowane odpowiednio do programu MSBuild `Configuration` i `OutputPath` właściwości.</span><span class="sxs-lookup"><span data-stu-id="065eb-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="065eb-154">Właściwości programu MSBuild można przekazywać przy użyciu jednego z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="065eb-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="065eb-155">Na przykład następujące polecenie publikuje `Release` kompilację do udziału sieciowego.</span><span class="sxs-lookup"><span data-stu-id="065eb-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="065eb-156">Udział sieciowy jest określony za pomocą ukośników (*//R8/*) i działa na wszystkich obsługiwanych platformach .NET Core.</span><span class="sxs-lookup"><span data-stu-id="065eb-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="065eb-157">Upewnij się, że opublikowana aplikacja do wdrożenia nie jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="065eb-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="065eb-158">Pliki w folderze *publikowania* są zablokowane, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="065eb-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="065eb-159">Nie można wykonać wdrożenia, ponieważ nie można skopiować zablokowanych plików.</span><span class="sxs-lookup"><span data-stu-id="065eb-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="065eb-160">Profile publikowania</span><span class="sxs-lookup"><span data-stu-id="065eb-160">Publish profiles</span></span>

<span data-ttu-id="065eb-161">W tej sekcji jest tworzony profil publikowania przy użyciu programu Visual Studio 2019 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="065eb-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="065eb-162">Po utworzeniu profilu publikowanie z programu Visual Studio lub wiersza polecenia jest dostępne.</span><span class="sxs-lookup"><span data-stu-id="065eb-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="065eb-163">Profile publikowania mogą uprościć proces publikowania i może istnieć dowolna liczba profilów.</span><span class="sxs-lookup"><span data-stu-id="065eb-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="065eb-164">Utwórz profil publikowania w programie Visual Studio, wybierając jedną z następujących ścieżek:</span><span class="sxs-lookup"><span data-stu-id="065eb-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="065eb-165">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="065eb-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="065eb-166">Wybierz pozycję **Publikuj {nazwa projektu}** z menu **kompilacja** .</span><span class="sxs-lookup"><span data-stu-id="065eb-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="065eb-167">Zostanie wyświetlona karta **Publikowanie** na stronie możliwości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="065eb-168">Jeśli projekt nie ma profilu publikowania, zostanie wyświetlona strona **Wybieranie elementu docelowego publikowania** .</span><span class="sxs-lookup"><span data-stu-id="065eb-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="065eb-169">Zostanie wyświetlony monit o wybranie jednego z następujących elementów docelowych publikowania:</span><span class="sxs-lookup"><span data-stu-id="065eb-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="065eb-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="065eb-170">Azure App Service</span></span>
* <span data-ttu-id="065eb-171">Azure App Service w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="065eb-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="065eb-172">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="065eb-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="065eb-173">Folder</span><span class="sxs-lookup"><span data-stu-id="065eb-173">Folder</span></span>
* <span data-ttu-id="065eb-174">IIS, FTP, Web Deploy (dla dowolnego serwera sieci Web)</span><span class="sxs-lookup"><span data-stu-id="065eb-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="065eb-175">Importuj profil</span><span class="sxs-lookup"><span data-stu-id="065eb-175">Import Profile</span></span>

<span data-ttu-id="065eb-176">Aby określić najbardziej odpowiedni cel publikowania, zobacz, [jakie opcje publikowania są odpowiednie dla mnie](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="065eb-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="065eb-177">Po wybraniu elementu docelowego publikowania **folderu** określ ścieżkę folderu do przechowywania opublikowanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="065eb-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="065eb-178">Domyślna ścieżka folderu to \*bin \\ {Konfiguracja projektu} \\ {Target Framework MONIKER} \publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="065eb-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="065eb-179">Na przykład \*bin\Release\netcoreapp2.2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="065eb-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="065eb-180">Wybierz przycisk **Utwórz profil** , aby zakończyć.</span><span class="sxs-lookup"><span data-stu-id="065eb-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="065eb-181">Po utworzeniu profilu publikowania zostanie zmieniona zawartość karty **Publikuj** .</span><span class="sxs-lookup"><span data-stu-id="065eb-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="065eb-182">Nowo utworzony profil zostanie wyświetlony na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="065eb-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="065eb-183">Poniżej listy rozwijanej wybierz pozycję **Utwórz nowy profil** , aby utworzyć inny nowy profil.</span><span class="sxs-lookup"><span data-stu-id="065eb-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="065eb-184">Narzędzie do publikowania programu Visual Studio tworzy *Właściwości/PublishProfiles/{profil}. pubxml* pliku MSBuild opisującego profil publikacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="065eb-185">Plik *. pubxml* :</span><span class="sxs-lookup"><span data-stu-id="065eb-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="065eb-186">Zawiera ustawienia konfiguracji publikowania i jest używana przez proces publikowania.</span><span class="sxs-lookup"><span data-stu-id="065eb-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="065eb-187">Można zmodyfikować, aby dostosować proces kompilowania i publikowania.</span><span class="sxs-lookup"><span data-stu-id="065eb-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="065eb-188">Podczas publikowania w usłudze Azure Target plik *. pubxml* zawiera identyfikator subskrypcji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="065eb-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="065eb-189">W przypadku tego typu docelowego nie jest zalecane Dodawanie tego pliku do kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="065eb-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="065eb-190">W przypadku publikowania w programie spoza platformy Azure, można bezpiecznie zaewidencjonować plik *. pubxml* .</span><span class="sxs-lookup"><span data-stu-id="065eb-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="065eb-191">Informacje poufne (na przykład publikowanie hasła) są szyfrowane na poziomie użytkownika/komputera.</span><span class="sxs-lookup"><span data-stu-id="065eb-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="065eb-192">Jest ona przechowywana w pliku *Properties/PublishProfiles/{Nazwa profilu}. pubxml. User* .</span><span class="sxs-lookup"><span data-stu-id="065eb-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="065eb-193">Ponieważ ten plik może przechowywać informacje poufne, nie należy go sprawdzać w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="065eb-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="065eb-194">Aby zapoznać się z omówieniem sposobu publikowania aplikacji internetowej ASP.NET Core, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="065eb-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="065eb-195">Zadania i elementy docelowe programu MSBuild wymagane do opublikowania aplikacji internetowej ASP.NET Core to "open source" w [repozytorium dotnet/websdk](https://github.com/dotnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="065eb-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [dotnet/websdk repository](https://github.com/dotnet/websdk).</span></span>

<span data-ttu-id="065eb-196">Następujące polecenia mogą używać profilów publikowania folderów, MSDeploy i [kudu](https://github.com/projectkudu/kudu/wiki) .</span><span class="sxs-lookup"><span data-stu-id="065eb-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="065eb-197">Ponieważ MSDeploy nie obsługuje obsługi wielu platform, następujące opcje MSDeploy są obsługiwane tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="065eb-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="065eb-198">**Folder (działa na wielu platformach):**</span><span class="sxs-lookup"><span data-stu-id="065eb-198">**Folder (works cross-platform):**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="065eb-199">**MSDeploy**</span><span class="sxs-lookup"><span data-stu-id="065eb-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="065eb-200">**Pakiet MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="065eb-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="065eb-201">W powyższych przykładach:</span><span class="sxs-lookup"><span data-stu-id="065eb-201">In the preceding examples:</span></span>

* <span data-ttu-id="065eb-202">`dotnet publish`i `dotnet build` obsługują interfejsy API kudu do publikowania na platformie Azure z dowolnej platformy.</span><span class="sxs-lookup"><span data-stu-id="065eb-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="065eb-203">Usługa Publish programu Visual Studio obsługuje interfejsy API kudu, ale jest obsługiwana przez WebSDK dla wieloplatformowego publikowania na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="065eb-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="065eb-204">Nie przekazuj `DeployOnBuild` do `dotnet publish` polecenia.</span><span class="sxs-lookup"><span data-stu-id="065eb-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="065eb-205">Aby uzyskać więcej informacji, zobacz [Microsoft. NET. Sdk. publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="065eb-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="065eb-206">Dodaj profil publikowania do folderu *Właściwości/PublishProfiles* projektu o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="065eb-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

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

## <a name="folder-publish-example"></a><span data-ttu-id="065eb-207">Przykład publikowania folderów</span><span class="sxs-lookup"><span data-stu-id="065eb-207">Folder publish example</span></span>

<span data-ttu-id="065eb-208">Podczas publikowania przy użyciu profilu o nazwie *FolderProfile*Użyj dowolnego z następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="065eb-208">When publishing with a profile named *FolderProfile*, use any of the following commands:</span></span>

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="065eb-209">Wywołanie polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) w interfejs wiersza polecenia platformy .NET Core, `msbuild` Aby uruchomić proces kompilowania i publikowania.</span><span class="sxs-lookup"><span data-stu-id="065eb-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="065eb-210">`dotnet build`Polecenia i `msbuild` są równoważne podczas przekazywania profilu folderu.</span><span class="sxs-lookup"><span data-stu-id="065eb-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="065eb-211">Podczas wywoływania `msbuild` bezpośrednio w systemie Windows jest używana .NET Framework wersja programu MSBuild.</span><span class="sxs-lookup"><span data-stu-id="065eb-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="065eb-212">Wywoływanie `dotnet build` w profilu nienależącym do folderu:</span><span class="sxs-lookup"><span data-stu-id="065eb-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="065eb-213">Wywołuje `msbuild` , który używa MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="065eb-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="065eb-214">Powoduje niepowodzenie (nawet w przypadku uruchamiania w systemie Windows).</span><span class="sxs-lookup"><span data-stu-id="065eb-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="065eb-215">Aby opublikować z profilem nienależącym do folderu, Połącz się `msbuild` bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="065eb-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="065eb-216">Następujący profil publikowania folderu został utworzony za pomocą programu Visual Studio i jest publikowany w udziale sieciowym:</span><span class="sxs-lookup"><span data-stu-id="065eb-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="065eb-217">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="065eb-217">In the preceding example:</span></span>

* <span data-ttu-id="065eb-218">`<ExcludeApp_Data>`Właściwość jest obecna tylko w celu spełnienia wymagań schematu XML.</span><span class="sxs-lookup"><span data-stu-id="065eb-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="065eb-219">`<ExcludeApp_Data>`Właściwość nie ma wpływu na proces publikowania, nawet jeśli w katalogu głównym projektu znajduje się folder *App_Data* .</span><span class="sxs-lookup"><span data-stu-id="065eb-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="065eb-220">Folder *App_Data* nie otrzymuje specjalnego traktowania, ponieważ w projektach ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="065eb-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>
* <span data-ttu-id="065eb-221">Właściwość `<LastUsedBuildConfiguration>` ma ustawioną wartość `Release`.</span><span class="sxs-lookup"><span data-stu-id="065eb-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="065eb-222">Podczas publikowania z programu Visual Studio, wartość `<LastUsedBuildConfiguration>` jest ustawiana za pomocą wartości podczas uruchamiania procesu publikowania.</span><span class="sxs-lookup"><span data-stu-id="065eb-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="065eb-223">`<LastUsedBuildConfiguration>`jest specjalne i nie należy go przesłaniać w zaimportowanym pliku MSBuild.</span><span class="sxs-lookup"><span data-stu-id="065eb-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="065eb-224">Tę właściwość można jednak zastąpić z wiersza polecenia przy użyciu jednego z poniższych metod.</span><span class="sxs-lookup"><span data-stu-id="065eb-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="065eb-225">Przy użyciu interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="065eb-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="065eb-226">Korzystanie z programu MSBuild:</span><span class="sxs-lookup"><span data-stu-id="065eb-226">Using MSBuild:</span></span>

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="065eb-227">Aby uzyskać więcej informacji, zobacz [MSBuild: jak ustawić właściwość Configuration](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="065eb-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="065eb-228">Publikowanie w punkcie końcowym MSDeploy z wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="065eb-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="065eb-229">Poniższy przykład używa ASP.NET Core aplikacji sieci Web utworzonej przez program Visual Studio o nazwie *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="065eb-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="065eb-230">Profil publikowania aplikacji platformy Azure jest dodawany razem z programem Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="065eb-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="065eb-231">Aby uzyskać więcej informacji na temat tworzenia profilu, zobacz sekcję [Publikowanie profilów](#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="065eb-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="065eb-232">Aby wdrożyć aplikację przy użyciu profilu publikowania, wykonaj `msbuild` polecenie z **wiersz polecenia dla deweloperów**programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="065eb-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="065eb-233">Wiersz polecenia jest dostępny w folderze programu *Visual Studio* menu **Start** na pasku zadań systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="065eb-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="065eb-234">Aby ułatwić dostęp, możesz dodać wiersz polecenia do menu **Narzędzia** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="065eb-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="065eb-235">Aby uzyskać więcej informacji, zobacz [wiersz polecenia dla deweloperów for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="065eb-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="065eb-236">Program MSBuild używa następującej składni polecenia:</span><span class="sxs-lookup"><span data-stu-id="065eb-236">MSBuild uses the following command syntax:</span></span>

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="065eb-237">`{PATH}`: Ścieżka do pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-237">`{PATH}`: Path to the app's project file.</span></span>
* <span data-ttu-id="065eb-238">`{PROFILE}`: Nazwa profilu publikowania.</span><span class="sxs-lookup"><span data-stu-id="065eb-238">`{PROFILE}`: Name of the publish profile.</span></span>
* <span data-ttu-id="065eb-239">`{USERNAME}`: Nazwa użytkownika MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="065eb-239">`{USERNAME}`: MSDeploy username.</span></span> <span data-ttu-id="065eb-240">`{USERNAME}`Można je znaleźć w profilu publikowania.</span><span class="sxs-lookup"><span data-stu-id="065eb-240">The `{USERNAME}` can be found in the publish profile.</span></span>
* <span data-ttu-id="065eb-241">`{PASSWORD}`: Hasło MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="065eb-241">`{PASSWORD}`: MSDeploy password.</span></span> <span data-ttu-id="065eb-242">Uzyskaj `{PASSWORD}` z poziomu usługi *{Profile}. Plik PublishSettings* .</span><span class="sxs-lookup"><span data-stu-id="065eb-242">Obtain the `{PASSWORD}` from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="065eb-243">Pobierz *. Plik PublishSettings* z:</span><span class="sxs-lookup"><span data-stu-id="065eb-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="065eb-244">**Eksplorator rozwiązań**: wybierz pozycję **Wyświetl**  >  **Eksplorator chmury**.</span><span class="sxs-lookup"><span data-stu-id="065eb-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="065eb-245">Połącz się ze swoją subskrypcją platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="065eb-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="065eb-246">Otwórz **App Services**.</span><span class="sxs-lookup"><span data-stu-id="065eb-246">Open **App Services**.</span></span> <span data-ttu-id="065eb-247">Kliknij prawym przyciskiem myszy aplikację.</span><span class="sxs-lookup"><span data-stu-id="065eb-247">Right-click the app.</span></span> <span data-ttu-id="065eb-248">Wybierz pozycję **Pobierz profil publikowania**.</span><span class="sxs-lookup"><span data-stu-id="065eb-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="065eb-249">Azure Portal: wybierz pozycję **Pobierz profil publikowania** w panelu **Przegląd** aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="065eb-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="065eb-250">W poniższym przykładzie zastosowano profil publikowania o nazwie *AzureWebApp-Web Deploy*:</span><span class="sxs-lookup"><span data-stu-id="065eb-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="065eb-251">Profilu publikacji można także użyć z poleceniem programu interfejs wiersza polecenia platformy .NET Core [dotnet](/dotnet/core/tools/dotnet-msbuild) z poziomu powłoki poleceń systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="065eb-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="065eb-252">`dotnet msbuild`Polecenie jest międzyplatformowym poleceniem i może kompilować ASP.NET Core aplikacje w systemach macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="065eb-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="065eb-253">Jednak program MSBuild w systemach macOS i Linux nie jest w stanie wdrożyć aplikacji na platformie Azure ani w innych punktach końcowych MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="065eb-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="065eb-254">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="065eb-254">Set the environment</span></span>

<span data-ttu-id="065eb-255">Dołącz `<EnvironmentName>` Właściwość w pliku profil publikacji (*pubxml*) lub plik projektu, aby ustawić [środowisko](xref:fundamentals/environments)aplikacji:</span><span class="sxs-lookup"><span data-stu-id="065eb-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="065eb-256">Jeśli potrzebujesz *web.config* transformacji (na przykład ustawienia zmiennych środowiskowych na podstawie konfiguracji, profilu lub środowiska), zobacz <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="065eb-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="065eb-257">Wyklucz pliki</span><span class="sxs-lookup"><span data-stu-id="065eb-257">Exclude files</span></span>

<span data-ttu-id="065eb-258">Podczas publikowania ASP.NET Core aplikacje sieci Web uwzględniane są następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="065eb-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="065eb-259">Kompiluj artefakty</span><span class="sxs-lookup"><span data-stu-id="065eb-259">Build artifacts</span></span>
* <span data-ttu-id="065eb-260">Foldery i pliki pasujące do następujących wzorców obsługi symboli wieloznacznych:</span><span class="sxs-lookup"><span data-stu-id="065eb-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="065eb-261">`**\*.config`(na przykład *web.config*)</span><span class="sxs-lookup"><span data-stu-id="065eb-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="065eb-262">`**\*.json`(na przykład *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="065eb-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="065eb-263">Program MSBuild obsługuje [wzorce obsługi symboli wieloznacznych](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="065eb-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="065eb-264">Na przykład poniższy `<Content>` element pomija kopiowanie plików tekstowych (*. txt*) w folderze *wwwroot\content* i jego podfolderach:</span><span class="sxs-lookup"><span data-stu-id="065eb-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="065eb-265">Powyższe oznakowanie można dodać do profilu publikowania lub pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="065eb-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="065eb-266">Po dodaniu do pliku *. csproj* reguła jest dodawana do wszystkich profilów publikacji w projekcie.</span><span class="sxs-lookup"><span data-stu-id="065eb-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="065eb-267">Następujący `<MsDeploySkipRules>` element wyklucza wszystkie pliki z folderu *wwwroot\content* :</span><span class="sxs-lookup"><span data-stu-id="065eb-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="065eb-268">`<MsDeploySkipRules>`nie można usunąć obiektów docelowych *pomijania* z lokacji wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="065eb-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="065eb-269">`<Content>`pliki i foldery wybrane są usuwane z lokacji wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="065eb-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="065eb-270">Załóżmy na przykład, że wdrożona aplikacja sieci Web miała następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="065eb-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="065eb-271">*Widoki/Home/About1. cshtml*</span><span class="sxs-lookup"><span data-stu-id="065eb-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="065eb-272">*Widoki/Home/About2. cshtml*</span><span class="sxs-lookup"><span data-stu-id="065eb-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="065eb-273">*Widoki/Home/About3. cshtml*</span><span class="sxs-lookup"><span data-stu-id="065eb-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="065eb-274">W przypadku `<MsDeploySkipRules>` dodania następujących elementów te pliki nie zostaną usunięte w lokacji wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="065eb-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="065eb-275">Poprzednie `<MsDeploySkipRules>` elementy uniemożliwiają wdrożenie *pominiętych* plików.</span><span class="sxs-lookup"><span data-stu-id="065eb-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="065eb-276">Te pliki nie zostaną usunięte po ich wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="065eb-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="065eb-277">Następujący `<Content>` element usuwa pliki dostosowane w lokacji wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="065eb-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="065eb-278">Użycie wdrożenia wiersza polecenia z poprzednim `<Content>` elementem daje w wyniku odmianę następujących danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="065eb-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="065eb-279">Pliki dołączane</span><span class="sxs-lookup"><span data-stu-id="065eb-279">Include files</span></span>

<span data-ttu-id="065eb-280">W poniższych sekcjach opisano różne podejścia do dołączania plików w czasie publikacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="065eb-281">[Ogólna sekcja dołączania plików](#general-file-inclusion) używa `DotNetPublishFiles` elementu, który jest dostarczany przez plik Opublikuj elementy docelowe w [zestawie SDK sieci Web](xref:razor-pages/web-sdk).</span><span class="sxs-lookup"><span data-stu-id="065eb-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="065eb-282">Sekcja [selektywne Dołączanie plików](#selective-file-inclusion) używa `ResolvedFileToPublish` elementu, który jest dostarczany przez plik Opublikuj elementy docelowe w [zestaw .NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span><span class="sxs-lookup"><span data-stu-id="065eb-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="065eb-283">Ponieważ zestaw SDK sieci Web zależy od zestaw .NET Core SDK, każdy element może być używany w ASP.NET Core projekcie.</span><span class="sxs-lookup"><span data-stu-id="065eb-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="065eb-284">Ogólny dołączenie plików</span><span class="sxs-lookup"><span data-stu-id="065eb-284">General file inclusion</span></span>

<span data-ttu-id="065eb-285">Poniższy przykład `<ItemGroup>` elementu pokazuje Kopiowanie folderu znajdującego się poza katalogiem projektu do folderu opublikowanej witryny.</span><span class="sxs-lookup"><span data-stu-id="065eb-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="065eb-286">Wszystkie pliki dodane do następujących znaczników `<ItemGroup>` są domyślnie uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="065eb-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="065eb-287">Poprzedzające znaczniki:</span><span class="sxs-lookup"><span data-stu-id="065eb-287">The preceding markup:</span></span>

* <span data-ttu-id="065eb-288">Można dodać do pliku *csproj* lub profilu publikacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="065eb-289">Jeśli zostanie ona dodana do pliku *. csproj* , jest zawarta w każdym profilu publikacji w projekcie.</span><span class="sxs-lookup"><span data-stu-id="065eb-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="065eb-290">Deklaruje `_CustomFiles` element do przechowywania plików zgodnych ze `Include` wzorcem obsługi symboli wieloznacznych atrybutu.</span><span class="sxs-lookup"><span data-stu-id="065eb-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="065eb-291">Folder *obrazów* , do którego odwołuje się wzorzec, znajduje się poza katalogiem projektu.</span><span class="sxs-lookup"><span data-stu-id="065eb-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="065eb-292">[Właściwość zastrzeżona](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)o nazwie jest `$(MSBuildProjectDirectory)` rozpoznawana jako ścieżka bezwzględna pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="065eb-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="065eb-293">Zawiera listę plików do `DotNetPublishFiles` elementu.</span><span class="sxs-lookup"><span data-stu-id="065eb-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="065eb-294">Domyślnie `<DestinationRelativePath>` element elementu jest pusty.</span><span class="sxs-lookup"><span data-stu-id="065eb-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="065eb-295">Wartość domyślna jest zastępowana w znaczniku i używa [dobrze znanych metadanych elementu](/visualstudio/msbuild/msbuild-well-known-item-metadata) , takich jak `%(RecursiveDir)` .</span><span class="sxs-lookup"><span data-stu-id="065eb-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="065eb-296">Tekst wewnętrzny reprezentuje folder *wwwroot/images* opublikowanej witryny.</span><span class="sxs-lookup"><span data-stu-id="065eb-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="065eb-297">Selektywne Dołączanie plików</span><span class="sxs-lookup"><span data-stu-id="065eb-297">Selective file inclusion</span></span>

<span data-ttu-id="065eb-298">Wyróżnione znaczniki w poniższym przykładzie pokazują:</span><span class="sxs-lookup"><span data-stu-id="065eb-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="065eb-299">Kopiowanie pliku znajdującego się poza projektem do folderu *wwwroot* opublikowanej witryny.</span><span class="sxs-lookup"><span data-stu-id="065eb-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="065eb-300">Nazwa pliku *ReadMe2.MD* jest utrzymywana.</span><span class="sxs-lookup"><span data-stu-id="065eb-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="065eb-301">Wykluczanie folderu *wwwroot\Content*</span><span class="sxs-lookup"><span data-stu-id="065eb-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="065eb-302">Z wyłączeniem *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="065eb-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="065eb-303">Poprzedni przykład używa `ResolvedFileToPublish` elementu, którego domyślnym zachowaniem jest zawsze kopiowanie plików dostarczonych w `Include` atrybucie do opublikowanej lokacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="065eb-304">Zastąp zachowanie domyślne, dołączając `<CopyToPublishDirectory>` element podrzędny z tekstem wewnętrznym obu `Never` lub `PreserveNewest` .</span><span class="sxs-lookup"><span data-stu-id="065eb-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="065eb-305">Przykład:</span><span class="sxs-lookup"><span data-stu-id="065eb-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="065eb-306">Aby uzyskać więcej przykładów wdrożenia, zobacz [plik Readme zestawu SDK sieci Web](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span><span class="sxs-lookup"><span data-stu-id="065eb-306">For more deployment samples, see the [Web SDK README file](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="065eb-307">Uruchom element docelowy przed opublikowaniem lub po nim</span><span class="sxs-lookup"><span data-stu-id="065eb-307">Run a target before or after publishing</span></span>

<span data-ttu-id="065eb-308">Wbudowane `BeforePublish` i `AfterPublish` docelowe cele wykonują obiekt docelowy przed lub po elemencie docelowym publikacji.</span><span class="sxs-lookup"><span data-stu-id="065eb-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="065eb-309">Dodaj następujące elementy do profilu publikowania, aby rejestrować komunikaty konsoli zarówno przed opublikowaniem, jak i po nim:</span><span class="sxs-lookup"><span data-stu-id="065eb-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="065eb-310">Publikowanie na serwerze za pomocą niezaufanego certyfikatu</span><span class="sxs-lookup"><span data-stu-id="065eb-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="065eb-311">Dodaj `<AllowUntrustedCertificate>` Właściwość o wartości `True` do profilu publikowania:</span><span class="sxs-lookup"><span data-stu-id="065eb-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="065eb-312">Usługa kudu</span><span class="sxs-lookup"><span data-stu-id="065eb-312">The Kudu service</span></span>

<span data-ttu-id="065eb-313">Aby wyświetlić pliki w Azure App Service wdrożenia aplikacji sieci Web, należy użyć [usługi kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="065eb-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="065eb-314">Dołącz `scm` token do nazwy aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="065eb-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="065eb-315">Przykład:</span><span class="sxs-lookup"><span data-stu-id="065eb-315">For example:</span></span>

| <span data-ttu-id="065eb-316">Adres URL</span><span class="sxs-lookup"><span data-stu-id="065eb-316">URL</span></span>                                    | <span data-ttu-id="065eb-317">Wynik</span><span class="sxs-lookup"><span data-stu-id="065eb-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="065eb-318">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="065eb-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="065eb-319">Usługa kudu</span><span class="sxs-lookup"><span data-stu-id="065eb-319">Kudu service</span></span> |

<span data-ttu-id="065eb-320">Wybierz element menu [konsoli debugowania](https://github.com/projectkudu/kudu/wiki/Kudu-console) , aby wyświetlić, edytować, usunąć lub dodać pliki.</span><span class="sxs-lookup"><span data-stu-id="065eb-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="065eb-321">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="065eb-321">Additional resources</span></span>

* <span data-ttu-id="065eb-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) upraszcza wdrażanie aplikacji sieci Web i witryn internetowych na serwerach usług IIS.</span><span class="sxs-lookup"><span data-stu-id="065eb-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="065eb-323">[Repozytorium GitHub zestawu SDK sieci Web](https://github.com/dotnet/websdk/issues): problemy z plikami i żądania wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="065eb-323">[Web SDK GitHub repository](https://github.com/dotnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="065eb-324">Publikowanie aplikacji sieci Web ASP.NET na maszynie wirtualnej platformy Azure z poziomu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="065eb-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
