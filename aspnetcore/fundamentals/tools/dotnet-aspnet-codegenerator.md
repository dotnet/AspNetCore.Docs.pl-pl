---
title: dotnet ASPNET-CodeGenerator polecenie
author: rick-anderson
description: ASP.NET Core projekty są szkieletami poleceń dotnet ASPNET-CodeGenerator.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: d29535ea0b4197443fd884aaa6e5b4b763d04fc7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920706"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="1e6a2-103">dotnet ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="1e6a2-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="1e6a2-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e6a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1e6a2-105">`dotnet aspnet-codegenerator` -Uruchamia aparat tworzenia szkieletów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="1e6a2-106">`dotnet aspnet-codegenerator` jest wymagany tylko dla szkieletu z wiersza polecenia, nie jest potrzebny do korzystania z szkieletów w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="1e6a2-107">Instalowanie i aktualizowanie ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="1e6a2-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="1e6a2-108">Zainstaluj [zestaw SDK platformy .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="1e6a2-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="1e6a2-109">`dotnet-aspnet-codegenerator` jest [globalnym narzędziem](/dotnet/core/tools/global-tools) , które musi być zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="1e6a2-110">Następujące polecenie instaluje najnowszą stabilną wersję `dotnet-aspnet-codegenerator` Narzędzia:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="1e6a2-111">Następujące polecenie aktualizuje `dotnet-aspnet-codegenerator` najnowszą stabilną wersję dostępną z zainstalowanych zestawów SDK platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="1e6a2-112">Odinstaluj ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="1e6a2-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="1e6a2-113">Może być konieczne odinstalowanie programu w `aspnet-codegenerator` celu rozwiązania problemów.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="1e6a2-114">Na przykład jeśli zainstalowano wersję zapoznawczą programu `aspnet-codegenerator` , należy ją odinstalować przed zainstalowaniem wydanej wersji.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="1e6a2-115">Następujące polecenia Odinstalowuje `dotnet-aspnet-codegenerator` Narzędzie i instaluje najnowszą stabilną wersję:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="1e6a2-116">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="1e6a2-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="1e6a2-117">Opis</span><span class="sxs-lookup"><span data-stu-id="1e6a2-117">Description</span></span>

<span data-ttu-id="1e6a2-118">`dotnet aspnet-codegenerator`Polecenie globalne uruchamia ASP.NET Core generatora kodu i aparacie tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="1e6a2-119">Argumenty</span><span class="sxs-lookup"><span data-stu-id="1e6a2-119">Arguments</span></span>

`generator`

<span data-ttu-id="1e6a2-120">Generator kodu do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-120">The code generator to run.</span></span> <span data-ttu-id="1e6a2-121">Dostępne są następujące generatory:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-121">The following generators are available:</span></span>

| <span data-ttu-id="1e6a2-122">Generator</span><span class="sxs-lookup"><span data-stu-id="1e6a2-122">Generator</span></span>  | <span data-ttu-id="1e6a2-123">Operacja</span><span class="sxs-lookup"><span data-stu-id="1e6a2-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="1e6a2-124">obszar</span><span class="sxs-lookup"><span data-stu-id="1e6a2-124">area</span></span>       | [<span data-ttu-id="1e6a2-125">Szkieletuje obszar</span><span class="sxs-lookup"><span data-stu-id="1e6a2-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="1e6a2-126">kontroler</span><span class="sxs-lookup"><span data-stu-id="1e6a2-126">controller</span></span> | [<span data-ttu-id="1e6a2-127">Tworzy szkielety kontrolera</span><span class="sxs-lookup"><span data-stu-id="1e6a2-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="1e6a2-128">identity</span><span class="sxs-lookup"><span data-stu-id="1e6a2-128">identity</span></span>   | [<span data-ttu-id="1e6a2-129">Szkielety Identity</span><span class="sxs-lookup"><span data-stu-id="1e6a2-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="1e6a2-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="1e6a2-130">razorpage</span></span>  | [<span data-ttu-id="1e6a2-131">Strony szkieletów Razor</span><span class="sxs-lookup"><span data-stu-id="1e6a2-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="1e6a2-132">widok</span><span class="sxs-lookup"><span data-stu-id="1e6a2-132">view</span></span>       | [<span data-ttu-id="1e6a2-133">Tworzy szkielety widoku</span><span class="sxs-lookup"><span data-stu-id="1e6a2-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="1e6a2-134">Opcje</span><span class="sxs-lookup"><span data-stu-id="1e6a2-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="1e6a2-135">Określa katalog pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="1e6a2-136">Definiuje konfigurację kompilacji.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-136">Defines the build configuration.</span></span> <span data-ttu-id="1e6a2-137">Wartość domyślna to `Debug`.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="1e6a2-138">[Platforma](/dotnet/standard/frameworks) docelowa do użycia.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="1e6a2-139">Na przykład `net46`.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="1e6a2-140">Ścieżka bazowa kompilacji.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="1e6a2-141">Drukuje krótką pomoc dla polecenia.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="1e6a2-142">Nie kompiluje projektu przed uruchomieniem.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-142">Doesn't build the project before running.</span></span> <span data-ttu-id="1e6a2-143">Również niejawnie ustawia `--no-restore` flagę.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="1e6a2-144">Określa ścieżkę do pliku projektu do uruchomienia (nazwa folderu lub pełna ścieżka).</span><span class="sxs-lookup"><span data-stu-id="1e6a2-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="1e6a2-145">Jeśli nie zostanie określony, domyślnie jest bieżącym katalogiem.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="1e6a2-146">Opcje generatora</span><span class="sxs-lookup"><span data-stu-id="1e6a2-146">Generator options</span></span>

<span data-ttu-id="1e6a2-147">Poniższe sekcje zawierają szczegółowe informacje o opcjach dostępnych dla obsługiwanych generatorów:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="1e6a2-148">Obszar</span><span class="sxs-lookup"><span data-stu-id="1e6a2-148">Area</span></span>
* <span data-ttu-id="1e6a2-149">Kontroler</span><span class="sxs-lookup"><span data-stu-id="1e6a2-149">Controller</span></span>
* Identity  
* <span data-ttu-id="1e6a2-150">Razorstrona</span><span class="sxs-lookup"><span data-stu-id="1e6a2-150">Razorpage</span></span>
* <span data-ttu-id="1e6a2-151">Widok</span><span class="sxs-lookup"><span data-stu-id="1e6a2-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="1e6a2-152">Opcje obszaru</span><span class="sxs-lookup"><span data-stu-id="1e6a2-152">Area options</span></span>

<span data-ttu-id="1e6a2-153">To narzędzie jest przeznaczone do ASP.NET Core projektów sieci Web z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="1e6a2-154">Nie jest ona przeznaczona dla Razor aplikacji stron.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="1e6a2-155">Użycie: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="1e6a2-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="1e6a2-156">Poprzednie polecenie generuje następujące foldery:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="1e6a2-157">*Obszary*</span><span class="sxs-lookup"><span data-stu-id="1e6a2-157">*Areas*</span></span>
  * <span data-ttu-id="1e6a2-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="1e6a2-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="1e6a2-159">*Kontrolery*</span><span class="sxs-lookup"><span data-stu-id="1e6a2-159">*Controllers*</span></span>
    * <span data-ttu-id="1e6a2-160">*Dane*</span><span class="sxs-lookup"><span data-stu-id="1e6a2-160">*Data*</span></span>
    * <span data-ttu-id="1e6a2-161">*Modele*</span><span class="sxs-lookup"><span data-stu-id="1e6a2-161">*Models*</span></span>
    * <span data-ttu-id="1e6a2-162">*Widoki*</span><span class="sxs-lookup"><span data-stu-id="1e6a2-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="1e6a2-163">Opcje kontrolera</span><span class="sxs-lookup"><span data-stu-id="1e6a2-163">Controller options</span></span>

<span data-ttu-id="1e6a2-164">W poniższej tabeli wymieniono opcje  `aspnet-codegenerator` `controller` i `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="1e6a2-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="1e6a2-165">W poniższej tabeli wymieniono opcje unikatowe dla  `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="1e6a2-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="1e6a2-166">Opcja</span><span class="sxs-lookup"><span data-stu-id="1e6a2-166">Option</span></span>                         | <span data-ttu-id="1e6a2-167">Opis</span><span class="sxs-lookup"><span data-stu-id="1e6a2-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="1e6a2-168">--ControllerName lub-Name</span><span class="sxs-lookup"><span data-stu-id="1e6a2-168">--controllerName or -name</span></span>      | <span data-ttu-id="1e6a2-169">Nazwa kontrolera.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="1e6a2-170">--useAsyncActions lub-async</span><span class="sxs-lookup"><span data-stu-id="1e6a2-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="1e6a2-171">Generuj akcje kontrolerów asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="1e6a2-172">--noviews lub NV</span><span class="sxs-lookup"><span data-stu-id="1e6a2-172">--noViews or -nv</span></span>               | <span data-ttu-id="1e6a2-173">**Nie Generuj żadnych** widoków.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="1e6a2-174">--restWithNoViews lub-API</span><span class="sxs-lookup"><span data-stu-id="1e6a2-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="1e6a2-175">Wygeneruj kontroler z interfejsem API REST.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="1e6a2-176">`noViews` Założono, że wszystkie opcje powiązane z widokiem są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="1e6a2-177">--readWriteActions lub-akcje</span><span class="sxs-lookup"><span data-stu-id="1e6a2-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="1e6a2-178">Generuj kontroler z akcjami odczytu/zapisu bez modelu.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="1e6a2-179">Użyj `-h` przełącznika, aby uzyskać pomoc dotyczącą `aspnet-codegenerator controller` polecenia:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="1e6a2-180">Aby zapoznać się z przykładem, zobacz Tworzenie [szkieletu modelu filmu](xref:tutorials/first-mvc-app/adding-model) `dotnet aspnet-codegenerator controller` .</span><span class="sxs-lookup"><span data-stu-id="1e6a2-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="1e6a2-181">Razorstrona</span><span class="sxs-lookup"><span data-stu-id="1e6a2-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="1e6a2-182">Razor Strony mogą być tworzone indywidualnie przez określenie nazwy nowej strony i szablonu do użycia.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="1e6a2-183">Obsługiwane są następujące szablony:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="1e6a2-184">Na przykład następujące polecenie używa polecenia Edytuj szablon do wygenerowania elementu *MyEdit.cshtml.cs* *. cshtml* i:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="1e6a2-185">Zazwyczaj nazwa szablonu i wygenerowanego pliku nie jest określona i tworzone są następujące szablony:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="1e6a2-186">W poniższej tabeli wymieniono opcje  `aspnet-codegenerator` `razorpage` i `controller` :</span><span class="sxs-lookup"><span data-stu-id="1e6a2-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="1e6a2-187">W poniższej tabeli wymieniono opcje unikatowe dla  `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="1e6a2-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="1e6a2-188">Opcja</span><span class="sxs-lookup"><span data-stu-id="1e6a2-188">Option</span></span>                        | <span data-ttu-id="1e6a2-189">Opis</span><span class="sxs-lookup"><span data-stu-id="1e6a2-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="1e6a2-190">--NamespaceName lub-Namespace</span><span class="sxs-lookup"><span data-stu-id="1e6a2-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="1e6a2-191">Nazwa przestrzeni nazw do użycia dla wygenerowanego PageModel</span><span class="sxs-lookup"><span data-stu-id="1e6a2-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="1e6a2-192">--partialView lub-częściowe</span><span class="sxs-lookup"><span data-stu-id="1e6a2-192">--partialView or -partial</span></span>     | <span data-ttu-id="1e6a2-193">Generuj widok częściowy.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-193">Generate a partial view.</span></span> <span data-ttu-id="1e6a2-194">Opcje układu-l i-UDL są ignorowane, jeśli jest określony.</span><span class="sxs-lookup"><span data-stu-id="1e6a2-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="1e6a2-195">--noPageModel lub-npm</span><span class="sxs-lookup"><span data-stu-id="1e6a2-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="1e6a2-196">Przełącz, aby nie generować klasy PageModel dla pustego szablonu</span><span class="sxs-lookup"><span data-stu-id="1e6a2-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="1e6a2-197">Użyj `-h` przełącznika, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="1e6a2-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="1e6a2-198">Aby zapoznać się z przykładem, zobacz Tworzenie [szkieletu modelu filmu](xref:tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage` .</span><span class="sxs-lookup"><span data-stu-id="1e6a2-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="1e6a2-199">Wyświetlanie [szkieletu Identity ](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="1e6a2-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
