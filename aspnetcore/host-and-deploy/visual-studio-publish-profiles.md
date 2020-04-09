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
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="c4cbb-103">Profile publikowania w programie Visual Studio (pubxml) dla ASP.NET wdrożenia aplikacji Core</span><span class="sxs-lookup"><span data-stu-id="c4cbb-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="c4cbb-104">Przez [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c4cbb-105">Ten dokument koncentruje się na użyciu programu Visual Studio 2019 lub nowszego do tworzenia i używania profilów publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="c4cbb-106">Profile publikowania utworzone za pomocą programu Visual Studio mogą być używane z MSBuild i Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="c4cbb-107">Aby uzyskać instrukcje dotyczące <xref:tutorials/publish-to-azure-webapp-using-vs>publikowania na platformie Azure, zobacz .</span><span class="sxs-lookup"><span data-stu-id="c4cbb-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="c4cbb-108">Polecenie `dotnet new mvc` tworzy plik projektu zawierający następujący [ \<element projektu>](/visualstudio/msbuild/project-element-msbuild)na poziomie głównym:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="c4cbb-109">Atrybut poprzedniego `<Project>` elementu importuje [właściwości](/visualstudio/msbuild/msbuild-properties) i [obiekty docelowe](/visualstudio/msbuild/msbuild-targets) MSBuild z *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* i *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets.* `Sdk`</span><span class="sxs-lookup"><span data-stu-id="c4cbb-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="c4cbb-110">Domyślną lokalizacją `$(MSBuildSDKsPath)` (z programem Visual Studio 2019 Enterprise) jest folder *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks.*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="c4cbb-111">`Microsoft.NET.Sdk.Web`(Web SDK) zależy od innych SDK, w tym `Microsoft.NET.Sdk` (.NET Core SDK) i `Microsoft.NET.Sdk.Razor` [(Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="c4cbb-112">Właściwości MSBuild i obiekty docelowe skojarzone z każdym zależnym sdk są importowane.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="c4cbb-113">Cele publikowania importują odpowiedni zestaw obiektów docelowych na podstawie użytej metody publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="c4cbb-114">Gdy MSBuild lub Visual Studio ładuje projekt, występują następujące akcje wysokiego poziomu:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="c4cbb-115">Zbuduj projekt</span><span class="sxs-lookup"><span data-stu-id="c4cbb-115">Build project</span></span>
* <span data-ttu-id="c4cbb-116">Oblicz pliki do opublikowania</span><span class="sxs-lookup"><span data-stu-id="c4cbb-116">Compute files to publish</span></span>
* <span data-ttu-id="c4cbb-117">Publikowanie plików w miejscu docelowym</span><span class="sxs-lookup"><span data-stu-id="c4cbb-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="c4cbb-118">Obliczanie elementów projektu</span><span class="sxs-lookup"><span data-stu-id="c4cbb-118">Compute project items</span></span>

<span data-ttu-id="c4cbb-119">Po załadowaniu projektu obliczane są [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (pliki).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="c4cbb-120">Typ elementu określa sposób przetwarzania pliku.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="c4cbb-121">Domyślnie pliki *cs* są uwzględniane `Compile` na liście elementów.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="c4cbb-122">Pliki na `Compile` liście elementów są kompilowane.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="c4cbb-123">Lista `Content` elementów zawiera pliki, które są publikowane oprócz wyjść kompilacji.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="c4cbb-124">Domyślnie pliki pasujące `wwwroot\**`do `**\*.config`wzorców `**\*.json` i są `Content` uwzględniane na liście elementów.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="c4cbb-125">Na przykład `wwwroot\**` [wzorzec globbingu](https://gruntjs.com/configuring-tasks#globbing-patterns) pasuje do wszystkich plików w folderze *wwwroot* i jego podfolderach.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c4cbb-126">Web SDK importuje [SDK Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c4cbb-127">W rezultacie pliki pasujące `**\*.cshtml` do `**\*.razor` wzorców i są `Content` również zawarte na liście elementów.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="c4cbb-128">Web SDK importuje [SDK Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c4cbb-129">W rezultacie pliki pasujące do `**\*.cshtml` wzorca `Content` są również uwzględniane na liście elementów.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="c4cbb-130">Aby jawnie dodać plik do listy publikowania, dodaj go bezpośrednio do pliku *csproj,* jak pokazano w sekcji [Dołącz pliki.](#include-files)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="c4cbb-131">Podczas wybierania przycisku **Publikuj** w programie Visual Studio lub podczas publikowania z wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="c4cbb-132">Właściwości/elementy są obliczane (pliki, które są potrzebne do kompilacji).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="c4cbb-133">**Tylko visual studio:** Pakiety NuGet są przywracane.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="c4cbb-134">(Przywracanie musi być jawne przez użytkownika w interfejsie wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="c4cbb-135">Projekt jest budowany.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-135">The project builds.</span></span>
* <span data-ttu-id="c4cbb-136">Elementy publikowania są obliczane (pliki, które są potrzebne do opublikowania).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="c4cbb-137">Projekt jest publikowany (obliczone pliki są kopiowane do miejsca docelowego publikowania).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="c4cbb-138">Gdy ASP.NET core projektu odwołania `Microsoft.NET.Sdk.Web` w pliku projektu, *plik app_offline.htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c4cbb-139">Gdy plik jest obecny, ASP.NET Moduł podstawowy bezpiecznie zamyka aplikację i obsługuje *plik app_offline.htm* podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c4cbb-140">Aby uzyskać więcej informacji, zobacz [ASP.NET odwołanie do konfiguracji modułu rdzenia](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="c4cbb-141">Podstawowe publikowanie wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="c4cbb-141">Basic command-line publishing</span></span>

<span data-ttu-id="c4cbb-142">Publikowanie wiersza polecenia działa na wszystkich platformach obsługiwanych przez platformę .NET Core i nie wymaga programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="c4cbb-143">W poniższych przykładach polecenie [dotnet .NET](/dotnet/core/tools/dotnet-publish) Core CLI jest uruchamiane z katalogu projektu (który zawiera plik *csproj).*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="c4cbb-144">Jeśli folder projektu nie jest bieżącym katalogiem roboczym, jawnie przekaż ścieżkę pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="c4cbb-145">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="c4cbb-146">Uruchom następujące polecenia, aby utworzyć i opublikować aplikację sieci web:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="c4cbb-147">Polecenie `dotnet publish` tworzy odmianę następujących danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="c4cbb-148">Domyślnym formatem folderu publikowania jest *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c4cbb-149">Na przykład *bin\Debug\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="c4cbb-150">Następujące polecenie określa `Release` kompilację i katalog publikowania:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="c4cbb-151">Polecenie `dotnet publish` wywołuje MSBuild, który `Publish` wywołuje obiekt docelowy.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="c4cbb-152">Wszystkie parametry `dotnet publish` przekazywane do są przekazywane do MSBuild.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="c4cbb-153">I `-c` `-o` parametry mapują odpowiednio właściwości `Configuration` i `OutputPath` MSBuild.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="c4cbb-154">MsBuild właściwości mogą być przekazywane przy użyciu jednego z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="c4cbb-155">Na przykład następujące polecenie publikuje `Release` kompilację do udziału sieciowego.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="c4cbb-156">Udział sieciowy jest określony z ukośnikami do przodu (*//r8/*) i działa na wszystkich obsługiwanych platformach .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="c4cbb-157">Upewnij się, że opublikowana aplikacja do wdrożenia nie jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="c4cbb-158">Pliki w folderze *publikowania* są blokowane, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="c4cbb-159">Nie można wywdrożenia, ponieważ nie można skopiować zablokowanych plików.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="c4cbb-160">Profile publikowania</span><span class="sxs-lookup"><span data-stu-id="c4cbb-160">Publish profiles</span></span>

<span data-ttu-id="c4cbb-161">W tej sekcji używa programu Visual Studio 2019 lub nowszego do utworzenia profilu publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="c4cbb-162">Po utworzeniu profilu publikowanie w programie Visual Studio lub wiersz polecenia jest dostępne.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="c4cbb-163">Profile publikowania mogą uprościć proces publikowania i może istnieć dowolna liczba profili.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="c4cbb-164">Utwórz profil publikowania w programie Visual Studio, wybierając jedną z następujących ścieżek:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="c4cbb-165">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="c4cbb-166">Z menu **Kompilacja** wybierz **polecenie Publikuj {PROJECT NAME}.**</span><span class="sxs-lookup"><span data-stu-id="c4cbb-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="c4cbb-167">Zostanie wyświetlona karta **Publikuj** na stronie Możliwości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="c4cbb-168">Jeśli projekt nie ma profilu publikowania, zostanie wyświetlona strona **Wybierz miejsce docelowe publikowania.**</span><span class="sxs-lookup"><span data-stu-id="c4cbb-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="c4cbb-169">Zostaniesz poproszony o wybranie jednego z następujących celów publikowania:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="c4cbb-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c4cbb-170">Azure App Service</span></span>
* <span data-ttu-id="c4cbb-171">Usługa Azure App Service w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="c4cbb-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="c4cbb-172">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="c4cbb-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="c4cbb-173">Folder</span><span class="sxs-lookup"><span data-stu-id="c4cbb-173">Folder</span></span>
* <span data-ttu-id="c4cbb-174">Usługi IIS, FTP, Web Deploy (dla dowolnego serwera sieci Web)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="c4cbb-175">Profil importu</span><span class="sxs-lookup"><span data-stu-id="c4cbb-175">Import Profile</span></span>

<span data-ttu-id="c4cbb-176">Aby określić najbardziej odpowiedni cel publikowania, zobacz [Jakie opcje publikowania są dla mnie odpowiednie.](/visualstudio/ide/not-in-toc/web-publish-options)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="c4cbb-177">Po wybraniu obiektu docelowego publikowania **folderu** należy określić ścieżkę folderu do przechowywania opublikowanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="c4cbb-178">Domyślną ścieżką folderu jest *bin\\{PROJECT CONFIGURATION}\\\\{TARGET FRAMEWORK MONIKER}\publish*.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c4cbb-179">Na przykład *bin\Release\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="c4cbb-180">Wybierz przycisk **Utwórz profil,** aby zakończyć.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="c4cbb-181">Po utworzeniu profilu publikowania zawartość karty **Publikowanie** zostanie ulegna zmianie.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="c4cbb-182">Nowo utworzony profil pojawi się na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="c4cbb-183">Poniżej listy rozwijanej wybierz pozycję **Utwórz nowy profil,** aby utworzyć inny nowy profil.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="c4cbb-184">Narzędzie publikowania w programie Visual Studio tworzy plik *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild opisujący profil publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="c4cbb-185">Plik *.pubxml:*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="c4cbb-186">Zawiera ustawienia konfiguracji publikowania i jest zużywany przez proces publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="c4cbb-187">Można zmodyfikować, aby dostosować proces kompilacji i publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="c4cbb-188">Podczas publikowania w witrynie docelowej platformy Azure plik *.pubxml* zawiera identyfikator subskrypcji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="c4cbb-189">W tym typie docelowym odradza się dodawanie tego pliku do kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="c4cbb-190">Podczas publikowania w celu docelowym spoza platformy Azure można bezpiecznie zaewidencjonować plik *pubxml.*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="c4cbb-191">Poufne informacje (takie jak hasło publikowania) są szyfrowane na poziomie użytkownika/komputera.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="c4cbb-192">Jest on przechowywany w pliku *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user.*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="c4cbb-193">Ponieważ ten plik może przechowywać poufne informacje, nie należy go zaewidencjonować do kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="c4cbb-194">Aby zapoznać się z omówieniem publikowania aplikacji <xref:host-and-deploy/index>sieci web ASP.NET Core, zobacz .</span><span class="sxs-lookup"><span data-stu-id="c4cbb-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="c4cbb-195">Zadania i cele MSBuild niezbędne do opublikowania ASP.NET core aplikacji sieci web są open-source w [repozytorium aspnet/websdk](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="c4cbb-196">Następujące polecenia mogą używać profilów publikowania folderów, MSDeploy i [Kudu.](https://github.com/projectkudu/kudu/wiki)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="c4cbb-197">Ponieważ MSDeploy nie obsługuje między platformami, następujące opcje MSDeploy są obsługiwane tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="c4cbb-198">**Folder (działa między platformami):**</span><span class="sxs-lookup"><span data-stu-id="c4cbb-198">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="c4cbb-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="c4cbb-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="c4cbb-200">**Pakiet MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="c4cbb-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="c4cbb-201">W poprzednich przykładach:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-201">In the preceding examples:</span></span>

* <span data-ttu-id="c4cbb-202">`dotnet publish`i `dotnet build` obsługuje interfejsy API Kudu do publikowania na platformie Azure z dowolnej platformy.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="c4cbb-203">Visual Studio publikowania obsługuje interfejsy API Kudu, ale jest obsługiwany przez WebSDK dla publikowania między platformami na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="c4cbb-204">Nie przekazuj `DeployOnBuild` `dotnet publish` do polecenia.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="c4cbb-205">Aby uzyskać więcej informacji, zobacz [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="c4cbb-206">Dodaj profil publikowania do folderu *Właściwości/Publikowanie profilu* projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

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

## <a name="folder-publish-example"></a><span data-ttu-id="c4cbb-207">Przykład publikowania folderów</span><span class="sxs-lookup"><span data-stu-id="c4cbb-207">Folder publish example</span></span>

<span data-ttu-id="c4cbb-208">Podczas publikowania z profilem o nazwie *FolderProfile*użyj jednego z następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-208">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="c4cbb-209">[Dotnet kompilacji polecenia .NET](/dotnet/core/tools/dotnet-build) Core `msbuild` CLI wywołuje wywołania polecenia kompilacji i publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="c4cbb-210">Polecenia `dotnet build` `msbuild` i są równoważne podczas przekazywania w profilu folderu.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="c4cbb-211">Podczas `msbuild` wywoływania bezpośrednio w systemie Windows używana jest wersja programu .NET Framework programu MSBuild.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="c4cbb-212">Wywoływanie `dotnet build` profilu bez folderu:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="c4cbb-213">Wywołuje `msbuild`, który używa MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="c4cbb-214">Powoduje błąd (nawet w przypadku pracy w systemie Windows).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="c4cbb-215">Aby opublikować z profilem `msbuild` spoza folderu, zadzwoń bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="c4cbb-216">Następujący profil publikowania folderów został utworzony za pomocą programu Visual Studio i publikuje w udziale sieciowym:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="c4cbb-217">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-217">In the preceding example:</span></span>

* <span data-ttu-id="c4cbb-218">Właściwość `<ExcludeApp_Data>` jest obecny tylko w celu spełnienia wymagań schematu XML.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="c4cbb-219">Właściwość `<ExcludeApp_Data>` nie ma wpływu na proces publikowania, nawet jeśli w katalogu głównym projektu znajduje się folder *App_Data.*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="c4cbb-220">Folder *App_Data* nie jest przedmiotem specjalnego traktowania, tak jak w projektach ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="c4cbb-221">Właściwość `<LastUsedBuildConfiguration>` jest `Release`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="c4cbb-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="c4cbb-222">Podczas publikowania z programu Visual `<LastUsedBuildConfiguration>` Studio wartość jest ustawiana przy użyciu wartości po uruchomieniu procesu publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="c4cbb-223">`<LastUsedBuildConfiguration>`jest specjalny i nie powinien być zastępowany w importowanym pliku MSBuild.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="c4cbb-224">Ta właściwość może jednak zostać zastąpiona z wiersza polecenia przy użyciu jednego z następujących metod.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="c4cbb-225">Korzystanie z interfejsu wiersza polecenia .NET Core:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="c4cbb-226">Korzystanie z usługi MSBuild:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-226">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="c4cbb-227">Aby uzyskać więcej informacji, zobacz [MSBuild: jak ustawić właściwość konfiguracji](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="c4cbb-228">Publikowanie w punkcie końcowym MSDeploy z wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="c4cbb-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="c4cbb-229">W poniższym przykładzie użyto aplikacji sieci web ASP.NET Core utworzonej przez program Visual Studio o nazwie *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="c4cbb-230">Profil publikowania usługi Azure Apps jest dodawany z programem Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="c4cbb-231">Aby uzyskać więcej informacji na temat tworzenia profilu, zobacz sekcję [Publikowanie profili.](#publish-profiles)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="c4cbb-232">Aby wdrożyć aplikację przy użyciu `msbuild` profilu publikowania, należy wykonać polecenie z **wiersza polecenia dewelopera**programu Visual Studio .</span><span class="sxs-lookup"><span data-stu-id="c4cbb-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="c4cbb-233">Wiersz polecenia jest dostępny w folderze *programu Visual Studio* menu **Start** na pasku zadań systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="c4cbb-234">Aby uzyskać łatwiejszy dostęp, można dodać wiersz polecenia do menu **Narzędzia** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="c4cbb-235">Aby uzyskać więcej informacji, zobacz [Wiersz polecenia dewelopera dla programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="c4cbb-236">MSBuild używa następującej składni polecenia:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-236">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="c4cbb-237">{ŚCIEŻKA} &ndash; Ścieżka do pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-237">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="c4cbb-238">{PROFIL} &ndash; Nazwa profilu publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-238">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="c4cbb-239">{NAZWA UŻYTKOWNIKA} &ndash; Nazwa użytkownika MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-239">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="c4cbb-240">{NAZWA UŻYTKOWNIKA} można znaleźć w profilu publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-240">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="c4cbb-241">{HASŁO} &ndash; MSDeploy hasło.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-241">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="c4cbb-242">Uzyskaj {HASŁO} z *{PROFILE}. PublishSettings* plik.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-242">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="c4cbb-243">Pobierz plik *. PublishSettings* plik z jednego z:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="c4cbb-244">**Eksplorator rozwiązań:** wybierz **Wyświetl** > **Eksploratora chmury**.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="c4cbb-245">Połącz się z subskrypcją platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="c4cbb-246">Otwórz **usługi aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-246">Open **App Services**.</span></span> <span data-ttu-id="c4cbb-247">Kliknij prawym przyciskiem myszy aplikację.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-247">Right-click the app.</span></span> <span data-ttu-id="c4cbb-248">Wybierz **pozycję Pobierz profil publikowania**.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="c4cbb-249">Portal Azure: Wybierz **pobierz profil publikowania** w panelu **Przegląd** aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="c4cbb-250">W poniższym przykładzie użyto profilu publikowania o nazwie *AzureWebApp — Wdrażanie w sieci Web:*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="c4cbb-251">Profil publikowania może być również używany z [poleceniem dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) .NET Core z powłoki poleceń systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="c4cbb-252">Polecenie `dotnet msbuild` jest poleceniem wieloplatformowym i może kompilować aplikacje ASP.NET Core w systemach macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="c4cbb-253">Jednak MSBuild w systemach macOS i Linux nie jest w stanie wdrożyć aplikację na platformie Azure lub innych punktów końcowych MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c4cbb-254">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="c4cbb-254">Set the environment</span></span>

<span data-ttu-id="c4cbb-255">Dołącz `<EnvironmentName>` właściwość do profilu publikowania (*pubxml*) lub pliku projektu, aby ustawić [środowisko](xref:fundamentals/environments)aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c4cbb-256">Jeśli wymagane są przekształcenia *web.config* (na przykład ustawienie zmiennych środowiskowych na <xref:host-and-deploy/iis/transform-webconfig>podstawie konfiguracji, profilu lub środowiska), zobacz .</span><span class="sxs-lookup"><span data-stu-id="c4cbb-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="c4cbb-257">Wykluczanie plików</span><span class="sxs-lookup"><span data-stu-id="c4cbb-257">Exclude files</span></span>

<span data-ttu-id="c4cbb-258">Podczas publikowania aplikacji sieci Web ASP.NET Core uwzględniane są następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="c4cbb-259">Buduj artefakty</span><span class="sxs-lookup"><span data-stu-id="c4cbb-259">Build artifacts</span></span>
* <span data-ttu-id="c4cbb-260">Foldery i pliki pasujące do następujących wzorców globbingu:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="c4cbb-261">`**\*.config`(na przykład *web.config*)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="c4cbb-262">`**\*.json`(na przykład *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="c4cbb-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="c4cbb-263">MSBuild obsługuje [wzorce globbingu](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="c4cbb-264">Na przykład następujący `<Content>` element pomija kopiowanie plików tekstowych (*txt*) w folderze *wwwroot\content* i jego podfolderach:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c4cbb-265">Poprzednie znaczniki można dodać do profilu publikowania lub pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="c4cbb-266">Po dodaniu do pliku *csproj* reguła jest dodawana do wszystkich profili publikowania w projekcie.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="c4cbb-267">Następujący `<MsDeploySkipRules>` element wyklucza wszystkie pliki z folderu *wwwroot\content:*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="c4cbb-268">`<MsDeploySkipRules>`nie spowoduje usunięcia obiektów docelowych *pomijania* z lokacji wdrażania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="c4cbb-269">`<Content>`pliki i foldery docelowe są usuwane z witryny wdrażania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="c4cbb-270">Załóżmy na przykład, że wdrożona aplikacja internetowa miała następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="c4cbb-271">*Widoki/Strona główna/Informacje1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="c4cbb-272">*Widoki/Strona główna/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="c4cbb-273">*Widoki/Strona główna/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="c4cbb-274">Jeśli zostaną `<MsDeploySkipRules>` dodane następujące elementy, te pliki nie zostaną usunięte w lokacji wdrażania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="c4cbb-275">Powyższe `<MsDeploySkipRules>` elementy uniemożliwiają *wdrażanie pominiętych* plików.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="c4cbb-276">Nie spowoduje to usunięcia tych plików po ich wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="c4cbb-277">Następujący `<Content>` element usuwa pliki docelowe w lokacji wdrażania:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c4cbb-278">Za pomocą wdrożenia wiersza `<Content>` polecenia z poprzednim elementem daje odmianę następujących danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="c4cbb-279">Pliki dołączane</span><span class="sxs-lookup"><span data-stu-id="c4cbb-279">Include files</span></span>

<span data-ttu-id="c4cbb-280">W poniższych sekcjach opisano różne podejścia do dołączania plików w czasie publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="c4cbb-281">[Sekcja Ogólne dołączanie](#general-file-inclusion) plików `DotNetPublishFiles` używa elementu, który jest dostarczany przez plik docelowy publikowania w web SDK.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the Web SDK.</span></span> <span data-ttu-id="c4cbb-282">Sekcja [Selektywne dołączanie](#selective-file-inclusion) `ResolvedFileToPublish` plików używa elementu, który jest dostarczany przez plik docelowy publikowania w pliku .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the .NET Core SDK.</span></span> <span data-ttu-id="c4cbb-283">Ponieważ składnik SDK sieci Web zależy od sdk .NET Core, każdy element może być używany w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="c4cbb-284">Ogólne dołączanie plików</span><span class="sxs-lookup"><span data-stu-id="c4cbb-284">General file inclusion</span></span>

<span data-ttu-id="c4cbb-285">Poniższy przykładowy `<ItemGroup>` element pokazuje kopiowanie folderu znajdującego się poza katalogiem projektu do folderu opublikowanej witryny.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="c4cbb-286">Wszystkie pliki dodane do następujących `<ItemGroup>` znaczników są domyślnie uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="c4cbb-287">Powyższe znaczniki:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-287">The preceding markup:</span></span>

* <span data-ttu-id="c4cbb-288">Można dodać do pliku *csproj* lub profilu publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="c4cbb-289">Jeśli zostanie dodany do pliku *csproj,* zostanie uwzględniony w każdym profilu publikowania w projekcie.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="c4cbb-290">Deklaruje `_CustomFiles` element do przechowywania plików `Include` pasujących do wzorca globbing atrybutu.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="c4cbb-291">Folder *obrazów,* do którego odwołuje się wzorzec, znajduje się poza katalogiem projektu.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="c4cbb-292">[Właściwość zastrzeżona](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), o nazwie, `$(MSBuildProjectDirectory)`jest rozpoznawana na ścieżce bezwzględnej pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="c4cbb-293">Zawiera listę plików do `DotNetPublishFiles` elementu.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="c4cbb-294">Domyślnie `<DestinationRelativePath>` element elementu jest pusty.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="c4cbb-295">Wartość domyślna jest zastępowana w znacznikach i używa dobrze znanych `%(RecursiveDir)` [metadanych elementu,](/visualstudio/msbuild/msbuild-well-known-item-metadata) takich jak .</span><span class="sxs-lookup"><span data-stu-id="c4cbb-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="c4cbb-296">Tekst wewnętrzny reprezentuje folder *wwwroot/images* opublikowanej witryny.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="c4cbb-297">Selektywne dołączanie plików</span><span class="sxs-lookup"><span data-stu-id="c4cbb-297">Selective file inclusion</span></span>

<span data-ttu-id="c4cbb-298">Wyróżnione znaczniki w poniższym przykładzie pokazuje:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="c4cbb-299">Kopiowanie pliku znajdującego się poza projektem do folderu *wwwroot* opublikowanej witryny.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="c4cbb-300">Nazwa pliku *ReadMe2.md* jest zachowywana.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="c4cbb-301">Z wyłączeniem folderu *wwwroot\Content.*</span><span class="sxs-lookup"><span data-stu-id="c4cbb-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="c4cbb-302">Z wyłączeniem *widoków\Strona główna\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="c4cbb-303">W poprzednim przykładzie `ResolvedFileToPublish` użyto elementu, którego domyślnym zachowaniem `Include` jest zawsze kopiowanie plików podanych w atrybucie do opublikowanej witryny.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="c4cbb-304">Zastądnie domyślne `<CopyToPublishDirectory>` zachowanie, dołączając element `Never` podrzędny z tekstem wewnętrznym jednego lub . `PreserveNewest`</span><span class="sxs-lookup"><span data-stu-id="c4cbb-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="c4cbb-305">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="c4cbb-306">Aby uzyskać więcej przykładów wdrożenia, zobacz [Readme repozytorium sdk sieci Web](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-306">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="c4cbb-307">Uruchamianie obiektu docelowego przed lub po opublikowaniu</span><span class="sxs-lookup"><span data-stu-id="c4cbb-307">Run a target before or after publishing</span></span>

<span data-ttu-id="c4cbb-308">Wbudowane `BeforePublish` i `AfterPublish` obiekty docelowe wykonać obiekt docelowy przed lub po docelowej publikowania.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="c4cbb-309">Dodaj następujące elementy do profilu publikowania, aby rejestrować komunikaty konsoli zarówno przed, jak i po opublikowaniu:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="c4cbb-310">Publikowanie na serwerze przy użyciu niezaufanego certyfikatu</span><span class="sxs-lookup"><span data-stu-id="c4cbb-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="c4cbb-311">Dodaj `<AllowUntrustedCertificate>` właściwość o `True` wartości do profilu publikowania:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="c4cbb-312">Usługa Kudu</span><span class="sxs-lookup"><span data-stu-id="c4cbb-312">The Kudu service</span></span>

<span data-ttu-id="c4cbb-313">Aby wyświetlić pliki we wdrożeniu aplikacji sieci Web usługi Azure App Service, użyj [usługi Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="c4cbb-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="c4cbb-314">Dołącz `scm` token do nazwy aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="c4cbb-315">Przykład:</span><span class="sxs-lookup"><span data-stu-id="c4cbb-315">For example:</span></span>

| <span data-ttu-id="c4cbb-316">Adres URL</span><span class="sxs-lookup"><span data-stu-id="c4cbb-316">URL</span></span>                                    | <span data-ttu-id="c4cbb-317">Wynik</span><span class="sxs-lookup"><span data-stu-id="c4cbb-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="c4cbb-318">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="c4cbb-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="c4cbb-319">Usługa Kudu</span><span class="sxs-lookup"><span data-stu-id="c4cbb-319">Kudu service</span></span> |

<span data-ttu-id="c4cbb-320">Wybierz element menu [Konsoli debugowania,](https://github.com/projectkudu/kudu/wiki/Kudu-console) aby wyświetlić, edytować, usunąć lub dodać pliki.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4cbb-321">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c4cbb-321">Additional resources</span></span>

* <span data-ttu-id="c4cbb-322">[Wdrażanie sieci Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) upraszcza wdrażanie aplikacji sieci Web i witryn sieci Web na serwerach usług IIS.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="c4cbb-323">[Repozytorium GitHub web SDK](https://github.com/aspnet/websdk/issues): Problemy z plikami i funkcje żądania do wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="c4cbb-323">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="c4cbb-324">Publikowanie ASP.NET aplikacji sieci Web na maszynie Wirtualnej platformy Azure z programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4cbb-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
