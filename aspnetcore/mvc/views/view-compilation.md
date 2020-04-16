---
title: Kompilacja plików brzytwy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak odbywa się kompilacja plików Razor w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440938"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="6c773-103">Kompilacja plików brzytwy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c773-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="6c773-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6c773-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="6c773-105">Pliki razor z rozszerzeniem *.cshtml* są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6c773-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="6c773-106">Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="6c773-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="6c773-107">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="6c773-107">Razor compilation</span></span>

<span data-ttu-id="6c773-108">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="6c773-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6c773-109">Po włączeniu kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając aktualizowanie plików Razor, jeśli są edytowane.</span><span class="sxs-lookup"><span data-stu-id="6c773-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="6c773-110">Włączanie kompilacji środowiska uruchomieniowego podczas tworzenia projektu</span><span class="sxs-lookup"><span data-stu-id="6c773-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="6c773-111">Strony Razor i szablony projektów MVC zawierają opcję włączania kompilacji środowiska uruchomieniowego podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="6c773-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="6c773-112">Ta opcja jest obsługiwana w ASP.NET Core 3.1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="6c773-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c773-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c773-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c773-114">W oknie **dialogowym Tworzenie nowej aplikacji sieci Web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="6c773-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="6c773-115">Wybierz szablon projektu **aplikacji sieci Web** lub aplikacji sieci Web **(Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="6c773-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="6c773-116">Zaznacz pole wyboru **Włącz kompilację środowiska uruchomieniowego Razor.**</span><span class="sxs-lookup"><span data-stu-id="6c773-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6c773-117">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c773-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6c773-118">Użyj `-rrc` opcji `--razor-runtime-compilation` lub szablonu.</span><span class="sxs-lookup"><span data-stu-id="6c773-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="6c773-119">Na przykład następujące polecenie tworzy nowy projekt Razor Pages z włączoną kompilacją środowiska wykonawczego:</span><span class="sxs-lookup"><span data-stu-id="6c773-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="6c773-120">Włączanie kompilacji środowiska uruchomieniowego w istniejącym projekcie</span><span class="sxs-lookup"><span data-stu-id="6c773-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="6c773-121">Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk w istniejącym projekcie:</span><span class="sxs-lookup"><span data-stu-id="6c773-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="6c773-122">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="6c773-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="6c773-123">Zaktualizuj `Startup.ConfigureServices` metodę projektu, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="6c773-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="6c773-124">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6c773-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="6c773-125">Warunkowe włączanie kompilacji środowiska uruchomieniowego w istniejącym projekcie</span><span class="sxs-lookup"><span data-stu-id="6c773-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="6c773-126">Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko dla rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="6c773-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="6c773-127">Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="6c773-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="6c773-128">Używa skompilowanych widoków.</span><span class="sxs-lookup"><span data-stu-id="6c773-128">Uses compiled views.</span></span>
* <span data-ttu-id="6c773-129">Nie włącza obserwatorów plików w produkcji.</span><span class="sxs-lookup"><span data-stu-id="6c773-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="6c773-130">Aby włączyć kompilację środowiska uruchomieniowego tylko w środowisku deweloperskim:</span><span class="sxs-lookup"><span data-stu-id="6c773-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="6c773-131">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="6c773-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="6c773-132">Zmodyfikuj sekcję profilu `environmentVariables` uruchamiania w *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6c773-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="6c773-133">Sprawdź `ASPNETCORE_ENVIRONMENT` jest `"Development"`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="6c773-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="6c773-134">Ustaw `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`na .</span><span class="sxs-lookup"><span data-stu-id="6c773-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="6c773-135">W poniższym przykładzie kompilacja środowiska uruchomieniowego jest `IIS Express` `RazorPagesApp` włączona w środowisku programistycznym dla profilów i uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="6c773-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="6c773-136">Żadne zmiany kodu nie są `Startup` potrzebne w klasie projektu.</span><span class="sxs-lookup"><span data-stu-id="6c773-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="6c773-137">W czasie wykonywania ASP.NET Core wyszukuje [atrybut HostingStartup](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`na poziomie zestawu w .</span><span class="sxs-lookup"><span data-stu-id="6c773-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="6c773-138">Atrybut `HostingStartup` określa kod startowy aplikacji do wykonania.</span><span class="sxs-lookup"><span data-stu-id="6c773-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="6c773-139">Ten kod startowy umożliwia kompilację środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="6c773-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="6c773-140">Włączanie kompilacji środowiska uruchomieniowego dla biblioteki klas Razor</span><span class="sxs-lookup"><span data-stu-id="6c773-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="6c773-141">Rozważmy scenariusz, w którym projekt Razor Pages odwołuje się do [biblioteki klas Razor (RCL)](xref:razor-pages/ui-class) o nazwie *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="6c773-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="6c773-142">RCL zawiera *plik _Layout.cshtml,* że wszystkie projekty MVC zespołu i Razor Pages zużywają.</span><span class="sxs-lookup"><span data-stu-id="6c773-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="6c773-143">Chcesz włączyć kompilację środowiska uruchomieniowego dla pliku *_Layout.cshtml* w tej śr.</span><span class="sxs-lookup"><span data-stu-id="6c773-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="6c773-144">W projekcie Strony maszynki do golenia wprowadzono następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="6c773-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="6c773-145">Włącz kompilację środowiska uruchomieniowego z instrukcjami w [warunkowo włączyć kompilację środowiska uruchomieniowego w istniejącym projekcie](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="6c773-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="6c773-146">Konfigurowanie opcji kompilacji środowiska `Startup.ConfigureServices`uruchomieniowego w:</span><span class="sxs-lookup"><span data-stu-id="6c773-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="6c773-147">W poprzednim kodzie jest skonstruowana ścieżka bezwzględna do listy RCL *MyClassLib.*</span><span class="sxs-lookup"><span data-stu-id="6c773-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="6c773-148">[Interfejs API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) służy do lokalizowania katalogów i plików w tej ścieżce bezwzględnej.</span><span class="sxs-lookup"><span data-stu-id="6c773-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="6c773-149">Na koniec `PhysicalFileProvider` wystąpienie jest dodawane do kolekcji dostawców plików, co umożliwia dostęp do plików *.cshtml* rcl.</span><span class="sxs-lookup"><span data-stu-id="6c773-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c773-150">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6c773-150">Additional resources</span></span>

* <span data-ttu-id="6c773-151">[Właściwości RazorCompileOnBuild i RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="6c773-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="6c773-152">Pliki razor z rozszerzeniem *.cshtml* są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6c773-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="6c773-153">Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6c773-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="6c773-154">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="6c773-154">Razor compilation</span></span>

<span data-ttu-id="6c773-155">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="6c773-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6c773-156">Po włączeniu kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając aktualizowanie plików Razor, jeśli są edytowane.</span><span class="sxs-lookup"><span data-stu-id="6c773-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="6c773-157">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="6c773-157">Runtime compilation</span></span>

<span data-ttu-id="6c773-158">Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk i trybów konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="6c773-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="6c773-159">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="6c773-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="6c773-160">Zaktualizuj `Startup.ConfigureServices` metodę projektu, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="6c773-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="6c773-161">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6c773-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="6c773-162">Warunkowe włączanie kompilacji środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="6c773-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="6c773-163">Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko dla rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="6c773-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="6c773-164">Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="6c773-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="6c773-165">Używa skompilowanych widoków.</span><span class="sxs-lookup"><span data-stu-id="6c773-165">Uses compiled views.</span></span>
* <span data-ttu-id="6c773-166">Jest mniejszy.</span><span class="sxs-lookup"><span data-stu-id="6c773-166">Is smaller in size.</span></span>
* <span data-ttu-id="6c773-167">Nie włącza obserwatorów plików w produkcji.</span><span class="sxs-lookup"><span data-stu-id="6c773-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="6c773-168">Aby włączyć kompilację środowiska uruchomieniowego opartą na środowisku i trybie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="6c773-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="6c773-169">Warunkowo odwołaj się do pakietu [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) na podstawie wartości aktywnej: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="6c773-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="6c773-170">Zaktualizuj `Startup.ConfigureServices` metodę projektu, `AddRazorRuntimeCompilation`aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="6c773-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="6c773-171">Warunkowo `AddRazorRuntimeCompilation` wykonać tak, że działa tylko `ASPNETCORE_ENVIRONMENT` w trybie `Development`debugowania, gdy zmienna jest ustawiona na:</span><span class="sxs-lookup"><span data-stu-id="6c773-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="6c773-172">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6c773-172">Additional resources</span></span>

* <span data-ttu-id="6c773-173">[Właściwości RazorCompileOnBuild i RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="6c773-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="6c773-174">Zobacz [przykład kompilacji środowiska wykonawczego w usłudze GitHub,](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) aby uzyskać przykład, który pokazuje tworzenie pracy kompilacji środowiska uruchomieniowego między projektami.</span><span class="sxs-lookup"><span data-stu-id="6c773-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6c773-175">Plik Razor jest kompilowany w czasie wykonywania, gdy wywoływana jest skojarzona strona Razor lub widok MVC.</span><span class="sxs-lookup"><span data-stu-id="6c773-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="6c773-176">Pliki razor są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6c773-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="6c773-177">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="6c773-177">Razor compilation</span></span>

<span data-ttu-id="6c773-178">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="6c773-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6c773-179">Edytowanie plików Razor po ich zaktualizowaniu jest obsługiwane w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="6c773-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="6c773-180">Domyślnie tylko skompilowana *plik Views.dll* i nie *.cshtml* plików lub odwołań zestawów wymaganych do kompilowania plików Razor są wdrażane z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="6c773-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6c773-181">Narzędzie do wstępnej kompilacji zostało przestarzałe i zostanie usunięte w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="6c773-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6c773-182">Zalecamy migrację do [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6c773-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="6c773-183">Zestaw Razor SDK jest skuteczny tylko wtedy, gdy w pliku projektu nie są ustawione żadne właściwości specyficzne dla prekompilacji.</span><span class="sxs-lookup"><span data-stu-id="6c773-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="6c773-184">Na przykład ustawienie `MvcRazorCompileOnPublish` właściwości pliku *csproj* `true` w celu wyłączenia SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="6c773-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="6c773-185">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="6c773-185">Runtime compilation</span></span>

<span data-ttu-id="6c773-186">Kompilacja w czasie kompilacji jest uzupełniona kompilacją środowiska wykonawczego plików Razor.</span><span class="sxs-lookup"><span data-stu-id="6c773-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="6c773-187">ASP.NET Core MVC ponownie skompiluje pliki Razor po zmianie zawartości pliku *cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6c773-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c773-188">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6c773-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
