---
title: Kompilacja plików brzytwy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak odbywa się kompilacja plików Razor w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277275"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="4e96c-103">Kompilacja plików brzytwy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e96c-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="4e96c-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e96c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="4e96c-105">Pliki razor z rozszerzeniem *.cshtml* są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="4e96c-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="4e96c-106">Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="4e96c-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="4e96c-107">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="4e96c-107">Razor compilation</span></span>

<span data-ttu-id="4e96c-108">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="4e96c-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="4e96c-109">Po włączeniu kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając aktualizowanie plików Razor, jeśli są edytowane.</span><span class="sxs-lookup"><span data-stu-id="4e96c-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="4e96c-110">Włączanie kompilacji środowiska uruchomieniowego podczas tworzenia projektu</span><span class="sxs-lookup"><span data-stu-id="4e96c-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="4e96c-111">Strony Razor i szablony projektów MVC zawierają opcję włączania kompilacji środowiska uruchomieniowego podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="4e96c-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="4e96c-112">Ta opcja jest obsługiwana w ASP.NET Core 3.1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="4e96c-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e96c-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e96c-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4e96c-114">W oknie **dialogowym Tworzenie nowej aplikacji sieci Web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="4e96c-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="4e96c-115">Wybierz szablon projektu **aplikacji sieci Web** lub aplikacji sieci Web **(Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="4e96c-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="4e96c-116">Zaznacz pole wyboru **Włącz kompilację środowiska uruchomieniowego Razor.**</span><span class="sxs-lookup"><span data-stu-id="4e96c-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4e96c-117">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="4e96c-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4e96c-118">Użyj `-rrc` opcji `--razor-runtime-compilation` lub szablonu.</span><span class="sxs-lookup"><span data-stu-id="4e96c-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="4e96c-119">Na przykład następujące polecenie tworzy nowy projekt Razor Pages z włączoną kompilacją środowiska wykonawczego:</span><span class="sxs-lookup"><span data-stu-id="4e96c-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="4e96c-120">Włączanie kompilacji środowiska uruchomieniowego w istniejącym projekcie</span><span class="sxs-lookup"><span data-stu-id="4e96c-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="4e96c-121">Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk w istniejącym projekcie:</span><span class="sxs-lookup"><span data-stu-id="4e96c-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="4e96c-122">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="4e96c-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="4e96c-123">Zaktualizuj `Startup.ConfigureServices` metodę projektu, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="4e96c-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="4e96c-124">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4e96c-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="4e96c-125">Warunkowe włączanie kompilacji środowiska uruchomieniowego w istniejącym projekcie</span><span class="sxs-lookup"><span data-stu-id="4e96c-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="4e96c-126">Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko dla rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="4e96c-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="4e96c-127">Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="4e96c-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="4e96c-128">Używa skompilowanych widoków.</span><span class="sxs-lookup"><span data-stu-id="4e96c-128">Uses compiled views.</span></span>
* <span data-ttu-id="4e96c-129">Nie włącza obserwatorów plików w produkcji.</span><span class="sxs-lookup"><span data-stu-id="4e96c-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="4e96c-130">Aby włączyć kompilację środowiska uruchomieniowego tylko w środowisku deweloperskim:</span><span class="sxs-lookup"><span data-stu-id="4e96c-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="4e96c-131">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="4e96c-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="4e96c-132">Zmodyfikuj sekcję profilu `environmentVariables` uruchamiania w *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4e96c-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="4e96c-133">Sprawdź `ASPNETCORE_ENVIRONMENT` jest `"Development"`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="4e96c-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="4e96c-134">Ustaw `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`na .</span><span class="sxs-lookup"><span data-stu-id="4e96c-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="4e96c-135">W poniższym przykładzie kompilacja środowiska uruchomieniowego jest `IIS Express` `RazorPagesApp` włączona w środowisku programistycznym dla profilów i uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="4e96c-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="4e96c-136">Żadne zmiany kodu nie są `Startup` potrzebne w klasie projektu.</span><span class="sxs-lookup"><span data-stu-id="4e96c-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="4e96c-137">W czasie wykonywania ASP.NET Core wyszukuje [atrybut HostingStartup](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`na poziomie zestawu w .</span><span class="sxs-lookup"><span data-stu-id="4e96c-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="4e96c-138">Atrybut `HostingStartup` określa kod startowy aplikacji do wykonania.</span><span class="sxs-lookup"><span data-stu-id="4e96c-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="4e96c-139">Ten kod startowy umożliwia kompilację środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="4e96c-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e96c-140">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4e96c-140">Additional resources</span></span>

* <span data-ttu-id="4e96c-141">[Właściwości RazorCompileOnBuild i RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="4e96c-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="4e96c-142">Zobacz [przykład kompilacji środowiska wykonawczego w usłudze GitHub,](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) aby uzyskać przykład, który pokazuje tworzenie pracy kompilacji środowiska uruchomieniowego między projektami.</span><span class="sxs-lookup"><span data-stu-id="4e96c-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="4e96c-143">Pliki razor z rozszerzeniem *.cshtml* są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="4e96c-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="4e96c-144">Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e96c-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="4e96c-145">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="4e96c-145">Razor compilation</span></span>

<span data-ttu-id="4e96c-146">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="4e96c-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="4e96c-147">Po włączeniu kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając aktualizowanie plików Razor, jeśli są edytowane.</span><span class="sxs-lookup"><span data-stu-id="4e96c-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="4e96c-148">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="4e96c-148">Runtime compilation</span></span>

<span data-ttu-id="4e96c-149">Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk i trybów konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="4e96c-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="4e96c-150">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="4e96c-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="4e96c-151">Zaktualizuj `Startup.ConfigureServices` metodę projektu, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="4e96c-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="4e96c-152">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4e96c-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="4e96c-153">Warunkowe włączanie kompilacji środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="4e96c-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="4e96c-154">Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko dla rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="4e96c-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="4e96c-155">Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="4e96c-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="4e96c-156">Używa skompilowanych widoków.</span><span class="sxs-lookup"><span data-stu-id="4e96c-156">Uses compiled views.</span></span>
* <span data-ttu-id="4e96c-157">Jest mniejszy.</span><span class="sxs-lookup"><span data-stu-id="4e96c-157">Is smaller in size.</span></span>
* <span data-ttu-id="4e96c-158">Nie włącza obserwatorów plików w produkcji.</span><span class="sxs-lookup"><span data-stu-id="4e96c-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="4e96c-159">Aby włączyć kompilację środowiska uruchomieniowego opartą na środowisku i trybie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="4e96c-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="4e96c-160">Warunkowo odwołaj się do pakietu [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) na podstawie wartości aktywnej: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="4e96c-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="4e96c-161">Zaktualizuj `Startup.ConfigureServices` metodę projektu, `AddRazorRuntimeCompilation`aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="4e96c-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="4e96c-162">Warunkowo `AddRazorRuntimeCompilation` wykonać tak, że działa tylko `ASPNETCORE_ENVIRONMENT` w trybie `Development`debugowania, gdy zmienna jest ustawiona na:</span><span class="sxs-lookup"><span data-stu-id="4e96c-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="4e96c-163">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4e96c-163">Additional resources</span></span>

* <span data-ttu-id="4e96c-164">[Właściwości RazorCompileOnBuild i RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="4e96c-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="4e96c-165">Zobacz [przykład kompilacji środowiska wykonawczego w usłudze GitHub,](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) aby uzyskać przykład, który pokazuje tworzenie pracy kompilacji środowiska uruchomieniowego między projektami.</span><span class="sxs-lookup"><span data-stu-id="4e96c-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e96c-166">Plik Razor jest kompilowany w czasie wykonywania, gdy wywoływana jest skojarzona strona Razor lub widok MVC.</span><span class="sxs-lookup"><span data-stu-id="4e96c-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="4e96c-167">Pliki razor są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="4e96c-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="4e96c-168">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="4e96c-168">Razor compilation</span></span>

<span data-ttu-id="4e96c-169">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="4e96c-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="4e96c-170">Edytowanie plików Razor po ich zaktualizowaniu jest obsługiwane w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="4e96c-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="4e96c-171">Domyślnie tylko skompilowana *plik Views.dll* i nie *.cshtml* plików lub odwołań zestawów wymaganych do kompilowania plików Razor są wdrażane z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="4e96c-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e96c-172">Narzędzie do wstępnej kompilacji zostało przestarzałe i zostanie usunięte w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4e96c-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="4e96c-173">Zalecamy migrację do [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="4e96c-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="4e96c-174">Zestaw Razor SDK jest skuteczny tylko wtedy, gdy w pliku projektu nie są ustawione żadne właściwości specyficzne dla prekompilacji.</span><span class="sxs-lookup"><span data-stu-id="4e96c-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="4e96c-175">Na przykład ustawienie `MvcRazorCompileOnPublish` właściwości pliku *csproj* `true` w celu wyłączenia SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="4e96c-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="4e96c-176">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="4e96c-176">Runtime compilation</span></span>

<span data-ttu-id="4e96c-177">Kompilacja w czasie kompilacji jest uzupełniona kompilacją środowiska wykonawczego plików Razor.</span><span class="sxs-lookup"><span data-stu-id="4e96c-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="4e96c-178">ASP.NET Core MVC ponownie skompiluje pliki Razor po zmianie zawartości pliku *cshtml.*</span><span class="sxs-lookup"><span data-stu-id="4e96c-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e96c-179">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4e96c-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
