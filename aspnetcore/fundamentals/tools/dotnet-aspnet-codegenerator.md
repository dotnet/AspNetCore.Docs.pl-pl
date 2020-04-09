---
title: dotnet aspnet-codegenerator, polecenie
author: rick-anderson
description: Szkielety poleceń dotnet aspnet-codegenerator ASP.NET projektów core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665187"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="9f646-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="9f646-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="9f646-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9f646-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9f646-105">`dotnet aspnet-codegenerator`- Uruchamia silnik rusztowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f646-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="9f646-106">`dotnet aspnet-codegenerator`jest wymagane tylko do szkieletu z wiersza polecenia, nie jest potrzebne do używania szkieletów z programem Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9f646-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="9f646-107">Ten artykuł dotyczy [sdk .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) i nowszych.</span><span class="sxs-lookup"><span data-stu-id="9f646-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="9f646-108">Instalacja aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="9f646-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="9f646-109">`dotnet-aspnet-codegenerator`jest [globalnym narzędziem,](/dotnet/core/tools/global-tools) które musi być zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="9f646-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="9f646-110">Następujące polecenie instaluje najnowszą `dotnet-aspnet-codegenerator` stabilną wersję narzędzia:</span><span class="sxs-lookup"><span data-stu-id="9f646-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="9f646-111">Następujące polecenie `dotnet-aspnet-codegenerator` jest aktualizowane w najnowszej stabilnej wersji dostępnej w zainstalowanych pakietach SDK rdzenia .NET:</span><span class="sxs-lookup"><span data-stu-id="9f646-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="9f646-112">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9f646-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="9f646-113">Opis</span><span class="sxs-lookup"><span data-stu-id="9f646-113">Description</span></span>

<span data-ttu-id="9f646-114">Globalne `dotnet aspnet-codegenerator` polecenie uruchamia generator kodu ASP.NET Core i silnik rusztowań.</span><span class="sxs-lookup"><span data-stu-id="9f646-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="9f646-115">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9f646-115">Arguments</span></span>

`generator`

<span data-ttu-id="9f646-116">Generator kodu do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="9f646-116">The code generator to run.</span></span> <span data-ttu-id="9f646-117">Dostępne są następujące generatory:</span><span class="sxs-lookup"><span data-stu-id="9f646-117">The following generators are available:</span></span>

| <span data-ttu-id="9f646-118">Generator</span><span class="sxs-lookup"><span data-stu-id="9f646-118">Generator</span></span> | <span data-ttu-id="9f646-119">Operacja</span><span class="sxs-lookup"><span data-stu-id="9f646-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="9f646-120">obszar</span><span class="sxs-lookup"><span data-stu-id="9f646-120">area</span></span>      | [<span data-ttu-id="9f646-121">Rusztowania obszar</span><span class="sxs-lookup"><span data-stu-id="9f646-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="9f646-122">kontroler</span><span class="sxs-lookup"><span data-stu-id="9f646-122">controller</span></span>| [<span data-ttu-id="9f646-123">Rusztowania kontrolera</span><span class="sxs-lookup"><span data-stu-id="9f646-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="9f646-124">identity</span><span class="sxs-lookup"><span data-stu-id="9f646-124">identity</span></span>  | [<span data-ttu-id="9f646-125">Tożsamość rusztowań</span><span class="sxs-lookup"><span data-stu-id="9f646-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="9f646-126">brzytwa</span><span class="sxs-lookup"><span data-stu-id="9f646-126">razorpage</span></span> | [<span data-ttu-id="9f646-127">Rusztowania Razor Strony</span><span class="sxs-lookup"><span data-stu-id="9f646-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="9f646-128">widok</span><span class="sxs-lookup"><span data-stu-id="9f646-128">view</span></span>      | [<span data-ttu-id="9f646-129">Rusztowania widoku</span><span class="sxs-lookup"><span data-stu-id="9f646-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="9f646-130">Opcje</span><span class="sxs-lookup"><span data-stu-id="9f646-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="9f646-131">Określa katalog pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="9f646-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="9f646-132">Definiuje konfigurację kompilacji.</span><span class="sxs-lookup"><span data-stu-id="9f646-132">Defines the build configuration.</span></span> <span data-ttu-id="9f646-133">Wartością domyślną jest `Debug`.</span><span class="sxs-lookup"><span data-stu-id="9f646-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="9f646-134">Struktura [docelowa](/dotnet/standard/frameworks) do użycia.</span><span class="sxs-lookup"><span data-stu-id="9f646-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="9f646-135">Na przykład `net46`.</span><span class="sxs-lookup"><span data-stu-id="9f646-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="9f646-136">Ścieżka podstawowa kompilacji.</span><span class="sxs-lookup"><span data-stu-id="9f646-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="9f646-137">Drukuje krótką pomoc dla polecenia.</span><span class="sxs-lookup"><span data-stu-id="9f646-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="9f646-138">Nie tworzy projektu przed uruchomieniem.</span><span class="sxs-lookup"><span data-stu-id="9f646-138">Doesn't build the project before running.</span></span> <span data-ttu-id="9f646-139">Również niejawnie `--no-restore` ustawia flagę.</span><span class="sxs-lookup"><span data-stu-id="9f646-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="9f646-140">Określa ścieżkę pliku projektu do uruchomienia (nazwę folderu lub pełną ścieżkę).</span><span class="sxs-lookup"><span data-stu-id="9f646-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="9f646-141">Jeśli nie zostanie określony, domyślnie jest to bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="9f646-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="9f646-142">Opcje generatora</span><span class="sxs-lookup"><span data-stu-id="9f646-142">Generator options</span></span>

<span data-ttu-id="9f646-143">W poniższych sekcjach wyszczególnia się opcjach dostępnych dla obsługiwanych generatorów:</span><span class="sxs-lookup"><span data-stu-id="9f646-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="9f646-144">Obszar</span><span class="sxs-lookup"><span data-stu-id="9f646-144">Area</span></span>
* <span data-ttu-id="9f646-145">Kontrolera</span><span class="sxs-lookup"><span data-stu-id="9f646-145">Controller</span></span>
* <span data-ttu-id="9f646-146">Tożsamość</span><span class="sxs-lookup"><span data-stu-id="9f646-146">Identity</span></span>  
* <span data-ttu-id="9f646-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="9f646-147">Razorpage</span></span>
* <span data-ttu-id="9f646-148">Widok</span><span class="sxs-lookup"><span data-stu-id="9f646-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="9f646-149">Opcje obszaru</span><span class="sxs-lookup"><span data-stu-id="9f646-149">Area options</span></span>

<span data-ttu-id="9f646-150">To narzędzie jest przeznaczone do ASP.NET podstawowych projektów sieci web z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="9f646-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="9f646-151">Nie jest przeznaczony dla aplikacji Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9f646-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="9f646-152">Użycie: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="9f646-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="9f646-153">Poprzednie polecenie generuje następujące foldery:</span><span class="sxs-lookup"><span data-stu-id="9f646-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="9f646-154">*Obszary*</span><span class="sxs-lookup"><span data-stu-id="9f646-154">*Areas*</span></span>
  * <span data-ttu-id="9f646-155">*Nazwaogeneracji Obszaru*</span><span class="sxs-lookup"><span data-stu-id="9f646-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="9f646-156">*Kontrolery*</span><span class="sxs-lookup"><span data-stu-id="9f646-156">*Controllers*</span></span>
    * <span data-ttu-id="9f646-157">*Dane*</span><span class="sxs-lookup"><span data-stu-id="9f646-157">*Data*</span></span>
    * <span data-ttu-id="9f646-158">*Modele*</span><span class="sxs-lookup"><span data-stu-id="9f646-158">*Models*</span></span>
    * <span data-ttu-id="9f646-159">*Widoki*</span><span class="sxs-lookup"><span data-stu-id="9f646-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="9f646-160">Opcje kontrolera</span><span class="sxs-lookup"><span data-stu-id="9f646-160">Controller options</span></span>

<span data-ttu-id="9f646-161">W poniższej tabeli wymieniono opcje dla: `aspnet-codegenerator` `controller` `razorpage`</span><span class="sxs-lookup"><span data-stu-id="9f646-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="9f646-162">W poniższej tabeli `aspnet-codegenerator controller`wymieniono opcje unikatowe dla:</span><span class="sxs-lookup"><span data-stu-id="9f646-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="9f646-163">Opcja</span><span class="sxs-lookup"><span data-stu-id="9f646-163">Option</span></span>               | <span data-ttu-id="9f646-164">Opis</span><span class="sxs-lookup"><span data-stu-id="9f646-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="9f646-165">--controllerName lub -name</span><span class="sxs-lookup"><span data-stu-id="9f646-165">--controllerName or -name</span></span> | <span data-ttu-id="9f646-166">Nazwa kontrolera.</span><span class="sxs-lookup"><span data-stu-id="9f646-166">Name of the controller.</span></span> |
| <span data-ttu-id="9f646-167">--useAsyncAkcje lub -asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="9f646-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="9f646-168">Generowanie akcji kontrolera asynchronii.</span><span class="sxs-lookup"><span data-stu-id="9f646-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="9f646-169">--noViews lub -nv</span><span class="sxs-lookup"><span data-stu-id="9f646-169">--noViews or -nv</span></span> | <span data-ttu-id="9f646-170">Nie **no** generuj żadnych widoków.</span><span class="sxs-lookup"><span data-stu-id="9f646-170">Generate **no** views.</span></span> |
| <span data-ttu-id="9f646-171">--restWithNoViews lub -api</span><span class="sxs-lookup"><span data-stu-id="9f646-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="9f646-172">Generowanie kontrolera za pomocą interfejsu API stylu REST.</span><span class="sxs-lookup"><span data-stu-id="9f646-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="9f646-173">`noViews`zakłada się, a wszystkie opcje związane z widokiem są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="9f646-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="9f646-174">--readWypisań lub -akcji</span><span class="sxs-lookup"><span data-stu-id="9f646-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="9f646-175">Generowanie kontrolera z akcjami odczytu/zapisu bez modelu.</span><span class="sxs-lookup"><span data-stu-id="9f646-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="9f646-176">Użyj `-h` przełącznika, aby `aspnet-codegenerator controller` uzyskać pomoc dotyczącą polecenia:</span><span class="sxs-lookup"><span data-stu-id="9f646-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="9f646-177">Zobacz [Scaffold modelu filmu](/aspnet/core/tutorials/razor-pages/model) na `dotnet aspnet-codegenerator controller`przykład .</span><span class="sxs-lookup"><span data-stu-id="9f646-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="9f646-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="9f646-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="9f646-179">Strony razor mogą być indywidualnie rusztowania, określając nazwę nowej strony i szablon do użycia.</span><span class="sxs-lookup"><span data-stu-id="9f646-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="9f646-180">Obsługiwane szablony to:</span><span class="sxs-lookup"><span data-stu-id="9f646-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="9f646-181">Na przykład następujące polecenie używa szablonu Edytuj do wygenerowania *pliku MyEdit.cshtml* i *MyEdit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="9f646-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="9f646-182">Zazwyczaj nie określono szablonu i wygenerowanej nazwy pliku i tworzone są następujące szablony:</span><span class="sxs-lookup"><span data-stu-id="9f646-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="9f646-183">W poniższej tabeli wymieniono opcje dla: `aspnet-codegenerator` `razorpage` `controller`</span><span class="sxs-lookup"><span data-stu-id="9f646-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="9f646-184">W poniższej tabeli `aspnet-codegenerator razorpage`wymieniono opcje unikatowe dla:</span><span class="sxs-lookup"><span data-stu-id="9f646-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="9f646-185">Opcja</span><span class="sxs-lookup"><span data-stu-id="9f646-185">Option</span></span>               | <span data-ttu-id="9f646-186">Opis</span><span class="sxs-lookup"><span data-stu-id="9f646-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="9f646-187">--namespaceName lub -obszar nazw</span><span class="sxs-lookup"><span data-stu-id="9f646-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="9f646-188">Nazwa obszaru nazw używanego dla wygenerowanego programu PageModel</span><span class="sxs-lookup"><span data-stu-id="9f646-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="9f646-189">--partialView lub -częściowe</span><span class="sxs-lookup"><span data-stu-id="9f646-189">--partialView or -partial</span></span> | <span data-ttu-id="9f646-190">Generowanie widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="9f646-190">Generate a partial view.</span></span> <span data-ttu-id="9f646-191">Opcje układu -l i -udl są ignorowane, jeśli jest to określone.</span><span class="sxs-lookup"><span data-stu-id="9f646-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="9f646-192">--noPageModel lub -npm</span><span class="sxs-lookup"><span data-stu-id="9f646-192">--noPageModel or -npm</span></span> | <span data-ttu-id="9f646-193">Przełączanie, aby nie generować klasy PageModel dla pustego szablonu</span><span class="sxs-lookup"><span data-stu-id="9f646-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="9f646-194">Użyj `-h` przełącznika, aby `aspnet-codegenerator razorpage` uzyskać pomoc dotyczącą polecenia:</span><span class="sxs-lookup"><span data-stu-id="9f646-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9f646-195">Zobacz [Scaffold modelu filmu](/aspnet/core/tutorials/razor-pages/model) na `dotnet aspnet-codegenerator razorpage`przykład .</span><span class="sxs-lookup"><span data-stu-id="9f646-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="9f646-196">Tożsamość</span><span class="sxs-lookup"><span data-stu-id="9f646-196">Identity</span></span>

<span data-ttu-id="9f646-197">Zobacz [Tożsamość rusztowania](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="9f646-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
