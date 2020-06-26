---
title: dotnet ASPNET-CodeGenerator polecenie
author: rick-anderson
description: ASP.NET Core projekty są szkieletami poleceń dotnet ASPNET-CodeGenerator.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: a106654c8a37e84e9186a2f06d90605df753e8a7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405604"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="e09da-103">dotnet ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="e09da-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="e09da-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e09da-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e09da-105">`dotnet aspnet-codegenerator`-Uruchamia aparat tworzenia szkieletów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e09da-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="e09da-106">`dotnet aspnet-codegenerator`jest wymagany tylko dla szkieletu z wiersza polecenia, nie jest potrzebny do korzystania z szkieletów w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e09da-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="e09da-107">Ten artykuł dotyczy [zestawu .NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) i nowszych wersji.</span><span class="sxs-lookup"><span data-stu-id="e09da-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="e09da-108">Instalowanie ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="e09da-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="e09da-109">`dotnet-aspnet-codegenerator`jest [globalnym narzędziem](/dotnet/core/tools/global-tools) , które musi być zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="e09da-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="e09da-110">Następujące polecenie instaluje najnowszą stabilną wersję `dotnet-aspnet-codegenerator` Narzędzia:</span><span class="sxs-lookup"><span data-stu-id="e09da-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e09da-111">Następujące polecenie aktualizuje `dotnet-aspnet-codegenerator` najnowszą stabilną wersję dostępną z zainstalowanych zestawów SDK platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e09da-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="e09da-112">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="e09da-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="e09da-113">Opis</span><span class="sxs-lookup"><span data-stu-id="e09da-113">Description</span></span>

<span data-ttu-id="e09da-114">`dotnet aspnet-codegenerator`Polecenie globalne uruchamia ASP.NET Core generatora kodu i aparacie tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="e09da-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="e09da-115">Argumenty</span><span class="sxs-lookup"><span data-stu-id="e09da-115">Arguments</span></span>

`generator`

<span data-ttu-id="e09da-116">Generator kodu do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="e09da-116">The code generator to run.</span></span> <span data-ttu-id="e09da-117">Dostępne są następujące generatory:</span><span class="sxs-lookup"><span data-stu-id="e09da-117">The following generators are available:</span></span>

| <span data-ttu-id="e09da-118">Generator</span><span class="sxs-lookup"><span data-stu-id="e09da-118">Generator</span></span> | <span data-ttu-id="e09da-119">Operacja</span><span class="sxs-lookup"><span data-stu-id="e09da-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="e09da-120">obszar</span><span class="sxs-lookup"><span data-stu-id="e09da-120">area</span></span>      | [<span data-ttu-id="e09da-121">Szkieletuje obszar</span><span class="sxs-lookup"><span data-stu-id="e09da-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="e09da-122">kontroler</span><span class="sxs-lookup"><span data-stu-id="e09da-122">controller</span></span>| [<span data-ttu-id="e09da-123">Tworzy szkielety kontrolera</span><span class="sxs-lookup"><span data-stu-id="e09da-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="e09da-124">identity</span><span class="sxs-lookup"><span data-stu-id="e09da-124">identity</span></span>  | <span data-ttu-id="e09da-125">[SzkieletyIdentity](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="e09da-125">[Scaffolds Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span> |
  <span data-ttu-id="e09da-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="e09da-126">razorpage</span></span> | <span data-ttu-id="e09da-127">[Strony szkieletów Razor](/aspnet/core/tutorials/razor-pages/model)</span><span class="sxs-lookup"><span data-stu-id="e09da-127">[Scaffolds Razor Pages](/aspnet/core/tutorials/razor-pages/model)</span></span> |
  <span data-ttu-id="e09da-128">widok</span><span class="sxs-lookup"><span data-stu-id="e09da-128">view</span></span>      | [<span data-ttu-id="e09da-129">Tworzy szkielety widoku</span><span class="sxs-lookup"><span data-stu-id="e09da-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="e09da-130">Opcje</span><span class="sxs-lookup"><span data-stu-id="e09da-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="e09da-131">Określa katalog pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="e09da-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="e09da-132">Definiuje konfigurację kompilacji.</span><span class="sxs-lookup"><span data-stu-id="e09da-132">Defines the build configuration.</span></span> <span data-ttu-id="e09da-133">Wartość domyślna to `Debug`.</span><span class="sxs-lookup"><span data-stu-id="e09da-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="e09da-134">[Platforma](/dotnet/standard/frameworks) docelowa do użycia.</span><span class="sxs-lookup"><span data-stu-id="e09da-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="e09da-135">Na przykład `net46`.</span><span class="sxs-lookup"><span data-stu-id="e09da-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="e09da-136">Ścieżka bazowa kompilacji.</span><span class="sxs-lookup"><span data-stu-id="e09da-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="e09da-137">Drukuje krótką pomoc dla polecenia.</span><span class="sxs-lookup"><span data-stu-id="e09da-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="e09da-138">Nie kompiluje projektu przed uruchomieniem.</span><span class="sxs-lookup"><span data-stu-id="e09da-138">Doesn't build the project before running.</span></span> <span data-ttu-id="e09da-139">Również niejawnie ustawia `--no-restore` flagę.</span><span class="sxs-lookup"><span data-stu-id="e09da-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="e09da-140">Określa ścieżkę do pliku projektu do uruchomienia (nazwa folderu lub pełna ścieżka).</span><span class="sxs-lookup"><span data-stu-id="e09da-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="e09da-141">Jeśli nie zostanie określony, domyślnie jest bieżącym katalogiem.</span><span class="sxs-lookup"><span data-stu-id="e09da-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="e09da-142">Opcje generatora</span><span class="sxs-lookup"><span data-stu-id="e09da-142">Generator options</span></span>

<span data-ttu-id="e09da-143">Poniższe sekcje zawierają szczegółowe informacje o opcjach dostępnych dla obsługiwanych generatorów:</span><span class="sxs-lookup"><span data-stu-id="e09da-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="e09da-144">Obszar</span><span class="sxs-lookup"><span data-stu-id="e09da-144">Area</span></span>
* <span data-ttu-id="e09da-145">Kontroler</span><span class="sxs-lookup"><span data-stu-id="e09da-145">Controller</span></span>
* Identity  
* <span data-ttu-id="e09da-146">Razorpage</span><span class="sxs-lookup"><span data-stu-id="e09da-146">Razorpage</span></span>
* <span data-ttu-id="e09da-147">Widok</span><span class="sxs-lookup"><span data-stu-id="e09da-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="e09da-148">Opcje obszaru</span><span class="sxs-lookup"><span data-stu-id="e09da-148">Area options</span></span>

<span data-ttu-id="e09da-149">To narzędzie jest przeznaczone do ASP.NET Core projektów sieci Web z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="e09da-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="e09da-150">Nie jest ona przeznaczona dla Razor aplikacji stron.</span><span class="sxs-lookup"><span data-stu-id="e09da-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="e09da-151">Użycie: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="e09da-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="e09da-152">Poprzednie polecenie generuje następujące foldery:</span><span class="sxs-lookup"><span data-stu-id="e09da-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="e09da-153">*Obszary*</span><span class="sxs-lookup"><span data-stu-id="e09da-153">*Areas*</span></span>
  * <span data-ttu-id="e09da-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="e09da-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="e09da-155">*Kontrolery*</span><span class="sxs-lookup"><span data-stu-id="e09da-155">*Controllers*</span></span>
    * <span data-ttu-id="e09da-156">*Dane*</span><span class="sxs-lookup"><span data-stu-id="e09da-156">*Data*</span></span>
    * <span data-ttu-id="e09da-157">*Modele*</span><span class="sxs-lookup"><span data-stu-id="e09da-157">*Models*</span></span>
    * <span data-ttu-id="e09da-158">*Widoki*</span><span class="sxs-lookup"><span data-stu-id="e09da-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="e09da-159">Opcje kontrolera</span><span class="sxs-lookup"><span data-stu-id="e09da-159">Controller options</span></span>

<span data-ttu-id="e09da-160">W poniższej tabeli wymieniono opcje `aspnet-codegenerator` `controller` i `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="e09da-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="e09da-161">W poniższej tabeli wymieniono opcje unikatowe dla `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="e09da-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="e09da-162">Opcja</span><span class="sxs-lookup"><span data-stu-id="e09da-162">Option</span></span>               | <span data-ttu-id="e09da-163">Opis</span><span class="sxs-lookup"><span data-stu-id="e09da-163">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="e09da-164">--ControllerName lub-Name</span><span class="sxs-lookup"><span data-stu-id="e09da-164">--controllerName or -name</span></span> | <span data-ttu-id="e09da-165">Nazwa kontrolera.</span><span class="sxs-lookup"><span data-stu-id="e09da-165">Name of the controller.</span></span> |
| <span data-ttu-id="e09da-166">--useAsyncActions lub-async</span><span class="sxs-lookup"><span data-stu-id="e09da-166">--useAsyncActions or -async</span></span> | <span data-ttu-id="e09da-167">Generuj akcje kontrolerów asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="e09da-167">Generate async controller actions.</span></span> |
| <span data-ttu-id="e09da-168">--noviews lub NV</span><span class="sxs-lookup"><span data-stu-id="e09da-168">--noViews or -nv</span></span> | <span data-ttu-id="e09da-169">**Nie Generuj żadnych** widoków.</span><span class="sxs-lookup"><span data-stu-id="e09da-169">Generate **no** views.</span></span> |
| <span data-ttu-id="e09da-170">--restWithNoViews lub-API</span><span class="sxs-lookup"><span data-stu-id="e09da-170">--restWithNoViews or -api</span></span>  | <span data-ttu-id="e09da-171">Wygeneruj kontroler z interfejsem API REST.</span><span class="sxs-lookup"><span data-stu-id="e09da-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="e09da-172">`noViews`Założono, że wszystkie opcje powiązane z widokiem są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="e09da-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="e09da-173">--readWriteActions lub-akcje</span><span class="sxs-lookup"><span data-stu-id="e09da-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="e09da-174">Generuj kontroler z akcjami odczytu/zapisu bez modelu.</span><span class="sxs-lookup"><span data-stu-id="e09da-174">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="e09da-175">Użyj `-h` przełącznika, aby uzyskać pomoc dotyczącą `aspnet-codegenerator controller` polecenia:</span><span class="sxs-lookup"><span data-stu-id="e09da-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="e09da-176">Aby zapoznać się z przykładem, zobacz Tworzenie [szkieletu modelu filmu](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator controller` .</span><span class="sxs-lookup"><span data-stu-id="e09da-176">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="e09da-177">Razorpage</span><span class="sxs-lookup"><span data-stu-id="e09da-177">Razorpage</span></span>

<a name="rp"></a>

Razor<span data-ttu-id="e09da-178">Strony mogą być tworzone indywidualnie przez określenie nazwy nowej strony i szablonu do użycia.</span><span class="sxs-lookup"><span data-stu-id="e09da-178"> Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="e09da-179">Obsługiwane są następujące szablony:</span><span class="sxs-lookup"><span data-stu-id="e09da-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="e09da-180">Na przykład następujące polecenie używa polecenia Edytuj szablon do wygenerowania elementu *MyEdit.cshtml.cs* *. cshtml* i:</span><span class="sxs-lookup"><span data-stu-id="e09da-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="e09da-181">Zazwyczaj nazwa szablonu i wygenerowanego pliku nie jest określona i tworzone są następujące szablony:</span><span class="sxs-lookup"><span data-stu-id="e09da-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="e09da-182">W poniższej tabeli wymieniono opcje `aspnet-codegenerator` `razorpage` i `controller` :</span><span class="sxs-lookup"><span data-stu-id="e09da-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="e09da-183">W poniższej tabeli wymieniono opcje unikatowe dla `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="e09da-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="e09da-184">Opcja</span><span class="sxs-lookup"><span data-stu-id="e09da-184">Option</span></span>               | <span data-ttu-id="e09da-185">Opis</span><span class="sxs-lookup"><span data-stu-id="e09da-185">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="e09da-186">--NamespaceName lub-Namespace</span><span class="sxs-lookup"><span data-stu-id="e09da-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="e09da-187">Nazwa przestrzeni nazw do użycia dla wygenerowanego PageModel</span><span class="sxs-lookup"><span data-stu-id="e09da-187">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="e09da-188">--partialView lub-częściowe</span><span class="sxs-lookup"><span data-stu-id="e09da-188">--partialView or -partial</span></span> | <span data-ttu-id="e09da-189">Generuj widok częściowy.</span><span class="sxs-lookup"><span data-stu-id="e09da-189">Generate a partial view.</span></span> <span data-ttu-id="e09da-190">Opcje układu-l i-UDL są ignorowane, jeśli jest określony.</span><span class="sxs-lookup"><span data-stu-id="e09da-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="e09da-191">--noPageModel lub-npm</span><span class="sxs-lookup"><span data-stu-id="e09da-191">--noPageModel or -npm</span></span> | <span data-ttu-id="e09da-192">Przełącz, aby nie generować klasy PageModel dla pustego szablonu</span><span class="sxs-lookup"><span data-stu-id="e09da-192">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="e09da-193">Użyj `-h` przełącznika, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="e09da-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e09da-194">Aby zapoznać się z przykładem, zobacz Tworzenie [szkieletu modelu filmu](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage` .</span><span class="sxs-lookup"><span data-stu-id="e09da-194">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="e09da-195">Wyświetlanie [szkieletu Identity ](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="e09da-195">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
