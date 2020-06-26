---
title: Razorkompilacja pliku w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kompilacja Razor plików występuje w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405448"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razor<span data-ttu-id="6eb63-103">kompilacja pliku w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6eb63-103"> file compilation in ASP.NET Core</span></span>

<span data-ttu-id="6eb63-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6eb63-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

Razor<span data-ttu-id="6eb63-105">Pliki z rozszerzeniem *. cshtml* są kompilowane w czasie kompilacji i publikowania przy użyciu [ Razor zestawu SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6eb63-105"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="6eb63-106">Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="6eb63-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="6eb63-107">kompilowa</span><span class="sxs-lookup"><span data-stu-id="6eb63-107"> compilation</span></span>

<span data-ttu-id="6eb63-108">Kompilacja plików w czasie kompilacji i czas publikowania Razor jest domyślnie włączona przez Razor zestaw SDK.</span><span class="sxs-lookup"><span data-stu-id="6eb63-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6eb63-109">Po włączeniu Kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając Razor aktualizowanie plików, jeśli są edytowane.</span><span class="sxs-lookup"><span data-stu-id="6eb63-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="6eb63-110">Włącz kompilację środowiska uruchomieniowego podczas tworzenia projektu</span><span class="sxs-lookup"><span data-stu-id="6eb63-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="6eb63-111">RazorStrony i szablony projektów MVC zawierają opcję włączenia kompilacji środowiska uruchomieniowego podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="6eb63-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="6eb63-112">Ta opcja jest obsługiwana w ASP.NET Core 3,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="6eb63-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6eb63-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eb63-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6eb63-114">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="6eb63-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="6eb63-115">Wybierz szablon projektu aplikacja sieci **Web** lub **aplikacja sieci Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="6eb63-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="6eb63-116">Zaznacz pole wyboru **Włącz Razor kompilację środowiska uruchomieniowego** .</span><span class="sxs-lookup"><span data-stu-id="6eb63-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6eb63-117">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="6eb63-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6eb63-118">Użyj `-rrc` `--razor-runtime-compilation` opcji szablonu lub.</span><span class="sxs-lookup"><span data-stu-id="6eb63-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="6eb63-119">Na przykład następujące polecenie tworzy nowy Razor Projekt strony z włączoną kompilacją środowiska uruchomieniowego:</span><span class="sxs-lookup"><span data-stu-id="6eb63-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="6eb63-120">Włącz kompilację środowiska uruchomieniowego w istniejącym projekcie</span><span class="sxs-lookup"><span data-stu-id="6eb63-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="6eb63-121">Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk w istniejącym projekcie:</span><span class="sxs-lookup"><span data-stu-id="6eb63-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="6eb63-122">Zainstaluj [pakiet Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pakiet NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="6eb63-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="6eb63-123">Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu dołączenia wywołania do <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="6eb63-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="6eb63-124">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="6eb63-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="6eb63-125">Warunkowe włączenie kompilacji środowiska uruchomieniowego w istniejącym projekcie</span><span class="sxs-lookup"><span data-stu-id="6eb63-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="6eb63-126">Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko na potrzeby lokalnego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="6eb63-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="6eb63-127">Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="6eb63-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="6eb63-128">Używa skompilowanych widoków.</span><span class="sxs-lookup"><span data-stu-id="6eb63-128">Uses compiled views.</span></span>
* <span data-ttu-id="6eb63-129">Nie włącza obserwatorów plików w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="6eb63-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="6eb63-130">Aby włączyć kompilację środowiska uruchomieniowego tylko w środowisku deweloperskim:</span><span class="sxs-lookup"><span data-stu-id="6eb63-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="6eb63-131">Zainstaluj [pakiet Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pakiet NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="6eb63-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="6eb63-132">Zmodyfikuj sekcję uruchamianie profilu `environmentVariables` w *launchSettings.jsna*:</span><span class="sxs-lookup"><span data-stu-id="6eb63-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="6eb63-133">Sprawdź `ASPNETCORE_ENVIRONMENT` , czy jest ustawiona na `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="6eb63-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="6eb63-134">Ustaw `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` wartość `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` .</span><span class="sxs-lookup"><span data-stu-id="6eb63-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="6eb63-135">W poniższym przykładzie Kompilacja środowiska uruchomieniowego jest włączona w środowisku programistycznym dla `IIS Express` profilów i `RazorPagesApp` uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="6eb63-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="6eb63-136">W klasie projektu nie są wymagane żadne zmiany w kodzie `Startup` .</span><span class="sxs-lookup"><span data-stu-id="6eb63-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="6eb63-137">W czasie wykonywania ASP.NET Core wyszukuje [atrybut HostingStartup na poziomie zestawu](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) w `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="6eb63-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="6eb63-138">Ten `HostingStartup` atrybut określa kod uruchomienia aplikacji do wykonania.</span><span class="sxs-lookup"><span data-stu-id="6eb63-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="6eb63-139">Ten kod uruchamiania umożliwia kompilację środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="6eb63-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="6eb63-140">Włącz kompilację środowiska uruchomieniowego dla Razor biblioteki klas</span><span class="sxs-lookup"><span data-stu-id="6eb63-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="6eb63-141">Rozważmy scenariusz, w którym Razor Projekt strony odwołuje się do [ Razor biblioteki klas (RCL)](xref:razor-pages/ui-class) o nazwie *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="6eb63-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="6eb63-142">RCL _Layout zawiera plik *. cshtml* , który jest używany przez wszystkie projekty MVC i Razor Pages zespołu.</span><span class="sxs-lookup"><span data-stu-id="6eb63-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="6eb63-143">Chcesz włączyć kompilację środowiska uruchomieniowego dla pliku *_Layout. cshtml* w tym RCL.</span><span class="sxs-lookup"><span data-stu-id="6eb63-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="6eb63-144">Wprowadź następujące zmiany w Razor projekcie stron:</span><span class="sxs-lookup"><span data-stu-id="6eb63-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="6eb63-145">Włącz kompilację środowiska uruchomieniowego z instrukcjami w [warunkowo Włącz kompilację środowiska uruchomieniowego w istniejącym projekcie](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="6eb63-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="6eb63-146">Skonfiguruj opcje kompilacji środowiska uruchomieniowego w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6eb63-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="6eb63-147">W poprzednim kodzie skonstruowana jest ścieżka bezwzględna do *MyClassLib* RCL.</span><span class="sxs-lookup"><span data-stu-id="6eb63-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="6eb63-148">[Interfejs API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) jest używany do lokalizowania katalogów i plików w tej ścieżce bezwzględnej.</span><span class="sxs-lookup"><span data-stu-id="6eb63-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="6eb63-149">Na koniec `PhysicalFileProvider` wystąpienie jest dodawane do kolekcji dostawców plików, co umożliwia dostęp do plików *. cshtml* RCL.</span><span class="sxs-lookup"><span data-stu-id="6eb63-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6eb63-150">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6eb63-150">Additional resources</span></span>

* <span data-ttu-id="6eb63-151">Właściwości [RazorCompileOnBuild i RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="6eb63-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor<span data-ttu-id="6eb63-152">Pliki z rozszerzeniem *. cshtml* są kompilowane w czasie kompilacji i publikowania przy użyciu [ Razor zestawu SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6eb63-152"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="6eb63-153">Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6eb63-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="6eb63-154">kompilowa</span><span class="sxs-lookup"><span data-stu-id="6eb63-154"> compilation</span></span>

<span data-ttu-id="6eb63-155">Kompilacja plików w czasie kompilacji i czas publikowania Razor jest domyślnie włączona przez Razor zestaw SDK.</span><span class="sxs-lookup"><span data-stu-id="6eb63-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6eb63-156">Po włączeniu Kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając Razor aktualizowanie plików, jeśli są edytowane.</span><span class="sxs-lookup"><span data-stu-id="6eb63-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="6eb63-157">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="6eb63-157">Runtime compilation</span></span>

<span data-ttu-id="6eb63-158">Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk i trybów konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="6eb63-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="6eb63-159">Zainstaluj [pakiet Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pakiet NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="6eb63-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="6eb63-160">Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu dołączenia wywołania do <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="6eb63-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="6eb63-161">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="6eb63-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="6eb63-162">Warunkowe włączenie kompilacji środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="6eb63-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="6eb63-163">Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko na potrzeby lokalnego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="6eb63-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="6eb63-164">Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="6eb63-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="6eb63-165">Używa skompilowanych widoków.</span><span class="sxs-lookup"><span data-stu-id="6eb63-165">Uses compiled views.</span></span>
* <span data-ttu-id="6eb63-166">Jest mniejszy niż rozmiar.</span><span class="sxs-lookup"><span data-stu-id="6eb63-166">Is smaller in size.</span></span>
* <span data-ttu-id="6eb63-167">Nie włącza obserwatorów plików w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="6eb63-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="6eb63-168">Aby włączyć kompilację środowiska uruchomieniowego na podstawie trybu środowiska i konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="6eb63-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="6eb63-169">Warunkowo odwołuje się do [Microsoft. AspNetCore. MVC. Razor .. Pakiet RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) na podstawie aktywnej `Configuration` wartości:</span><span class="sxs-lookup"><span data-stu-id="6eb63-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="6eb63-170">Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu dołączenia wywołania do `AddRazorRuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="6eb63-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="6eb63-171">Warunkowo `AddRazorRuntimeCompilation` uruchamiaj w taki sposób, że działa tylko w trybie debugowania, gdy `ASPNETCORE_ENVIRONMENT` zmienna jest ustawiona na `Development` :</span><span class="sxs-lookup"><span data-stu-id="6eb63-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="6eb63-172">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6eb63-172">Additional resources</span></span>

* <span data-ttu-id="6eb63-173">Właściwości [RazorCompileOnBuild i RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="6eb63-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="6eb63-174">Zapoznaj się z przykładem [Kompilacja środowiska uruchomieniowego w witrynie GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) , aby uzyskać przykład pokazujący, jak działa Kompilacja środowiska uruchomieniowego między projektami.</span><span class="sxs-lookup"><span data-stu-id="6eb63-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6eb63-175">RazorPlik jest kompilowany w czasie wykonywania, gdy Razor zostanie wywołana skojarzona Strona lub widok MVC.</span><span class="sxs-lookup"><span data-stu-id="6eb63-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> Razor<span data-ttu-id="6eb63-176">pliki są kompilowane w czasie kompilacji i publikowania przy użyciu [ Razor zestawu SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6eb63-176"> files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="6eb63-177">kompilowa</span><span class="sxs-lookup"><span data-stu-id="6eb63-177"> compilation</span></span>

<span data-ttu-id="6eb63-178">Kompilacja i czas publikowania Razor plików są domyślnie włączone przez Razor zestaw SDK.</span><span class="sxs-lookup"><span data-stu-id="6eb63-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="6eb63-179">Edytowanie Razor plików po ich aktualizacji jest obsługiwane w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="6eb63-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="6eb63-180">Domyślnie tylko skompilowane pliki *Views.dll* i No *. cshtml* lub zestawy odwołań wymagane do kompilowania Razor plików są wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6eb63-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6eb63-181">Narzędzie prekompilacji jest przestarzałe i zostanie usunięte w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="6eb63-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6eb63-182">Zalecamy Migrowanie do [ Razor zestawu SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="6eb63-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="6eb63-183">RazorZestaw SDK działa tylko wtedy, gdy w pliku projektu nie są ustawione właściwości specyficzne dla prekompilacji.</span><span class="sxs-lookup"><span data-stu-id="6eb63-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="6eb63-184">Na przykład ustawienie właściwości pliku *. csproj* powoduje `MvcRazorCompileOnPublish` `true` wyłączenie Razor zestawu SDK.</span><span class="sxs-lookup"><span data-stu-id="6eb63-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="6eb63-185">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="6eb63-185">Runtime compilation</span></span>

<span data-ttu-id="6eb63-186">Kompilacja w czasie kompilacji jest uzupełniana przez kompilację plików w czasie wykonywania Razor .</span><span class="sxs-lookup"><span data-stu-id="6eb63-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="6eb63-187">ASP.NET Core MVC ponownie kompiluje Razor pliki, gdy zostanie zmieniona zawartość pliku *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="6eb63-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6eb63-188">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6eb63-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
