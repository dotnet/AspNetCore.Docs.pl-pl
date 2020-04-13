---
title: Kompilacja plików brzytwy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak odbywa się kompilacja plików Razor w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223962"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="7ea50-103">Kompilacja plików brzytwy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ea50-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="7ea50-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ea50-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ea50-105">Pliki razor z rozszerzeniem *.cshtml* są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="7ea50-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="7ea50-106">Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7ea50-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="7ea50-107">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="7ea50-107">Razor compilation</span></span>

<span data-ttu-id="7ea50-108">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="7ea50-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="7ea50-109">Po włączeniu kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając aktualizowanie plików Razor, jeśli są edytowane.</span><span class="sxs-lookup"><span data-stu-id="7ea50-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="7ea50-110">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="7ea50-110">Runtime compilation</span></span>

<span data-ttu-id="7ea50-111">Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk i trybów konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7ea50-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="7ea50-112">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="7ea50-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="7ea50-113">Zaktualizuj `Startup.ConfigureServices` metodę projektu, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="7ea50-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="7ea50-114">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7ea50-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="7ea50-115">Warunkowe włączanie kompilacji środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="7ea50-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="7ea50-116">Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko dla rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="7ea50-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="7ea50-117">Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="7ea50-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="7ea50-118">Używa skompilowanych widoków.</span><span class="sxs-lookup"><span data-stu-id="7ea50-118">Uses compiled views.</span></span>
* <span data-ttu-id="7ea50-119">Jest mniejszy.</span><span class="sxs-lookup"><span data-stu-id="7ea50-119">Is smaller in size.</span></span>
* <span data-ttu-id="7ea50-120">Nie włącza obserwatorów plików w produkcji.</span><span class="sxs-lookup"><span data-stu-id="7ea50-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="7ea50-121">Aby włączyć kompilację środowiska uruchomieniowego opartą na środowisku i trybie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7ea50-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="7ea50-122">Warunkowo odwołaj się do pakietu [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) na podstawie wartości aktywnej: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="7ea50-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="7ea50-123">Zaktualizuj `Startup.ConfigureServices` metodę projektu, `AddRazorRuntimeCompilation`aby uwzględnić wywołanie .</span><span class="sxs-lookup"><span data-stu-id="7ea50-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="7ea50-124">Warunkowo `AddRazorRuntimeCompilation` wykonać tak, że działa tylko `ASPNETCORE_ENVIRONMENT` w trybie `Development`debugowania, gdy zmienna jest ustawiona na:</span><span class="sxs-lookup"><span data-stu-id="7ea50-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="7ea50-125">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7ea50-125">Additional resources</span></span>

* <span data-ttu-id="7ea50-126">[Właściwości RazorCompileOnBuild i RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="7ea50-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="7ea50-127">Zobacz [przykład kompilacji środowiska wykonawczego w usłudze GitHub,](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) aby uzyskać przykład, który pokazuje tworzenie pracy kompilacji środowiska uruchomieniowego między projektami.</span><span class="sxs-lookup"><span data-stu-id="7ea50-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ea50-128">Plik Razor jest kompilowany w czasie wykonywania, gdy wywoływana jest skojarzona strona Razor lub widok MVC.</span><span class="sxs-lookup"><span data-stu-id="7ea50-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="7ea50-129">Pliki razor są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="7ea50-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="7ea50-130">Kompilacja maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="7ea50-130">Razor compilation</span></span>

<span data-ttu-id="7ea50-131">Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="7ea50-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="7ea50-132">Edytowanie plików Razor po ich zaktualizowaniu jest obsługiwane w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="7ea50-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="7ea50-133">Domyślnie tylko skompilowana *plik Views.dll* i nie *.cshtml* plików lub odwołań zestawów wymaganych do kompilowania plików Razor są wdrażane z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7ea50-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7ea50-134">Narzędzie do wstępnej kompilacji zostało przestarzałe i zostanie usunięte w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7ea50-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="7ea50-135">Zalecamy migrację do [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="7ea50-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="7ea50-136">Zestaw Razor SDK jest skuteczny tylko wtedy, gdy w pliku projektu nie są ustawione żadne właściwości specyficzne dla prekompilacji.</span><span class="sxs-lookup"><span data-stu-id="7ea50-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="7ea50-137">Na przykład ustawienie `MvcRazorCompileOnPublish` właściwości pliku *csproj* `true` w celu wyłączenia SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="7ea50-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="7ea50-138">Kompilacja środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="7ea50-138">Runtime compilation</span></span>

<span data-ttu-id="7ea50-139">Kompilacja w czasie kompilacji jest uzupełniona kompilacją środowiska wykonawczego plików Razor.</span><span class="sxs-lookup"><span data-stu-id="7ea50-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="7ea50-140">ASP.NET Core MVC ponownie skompiluje pliki Razor po zmianie zawartości pliku *cshtml.*</span><span class="sxs-lookup"><span data-stu-id="7ea50-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ea50-141">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7ea50-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
