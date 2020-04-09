---
title: Używanie ASP.NET podstawowych interfejsów API w bibliotece klas
author: scottaddie
description: Dowiedz się, jak używać ASP.NET podstawowych interfejsów API w bibliotece klas.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977200"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="8a70c-103">Używanie ASP.NET podstawowych interfejsów API w bibliotece klas</span><span class="sxs-lookup"><span data-stu-id="8a70c-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="8a70c-104">Przez [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8a70c-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8a70c-105">Ten dokument zawiera wskazówki dotyczące używania ASP.NET podstawowych interfejsów API w bibliotece klas.</span><span class="sxs-lookup"><span data-stu-id="8a70c-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="8a70c-106">Aby uzyskać wszystkie inne wskazówki dotyczące biblioteki, zobacz [Wskazówki dotyczące biblioteki open source](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="8a70c-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="8a70c-107">Określanie, które ASP.NET wersje Core do obsługi</span><span class="sxs-lookup"><span data-stu-id="8a70c-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="8a70c-108">ASP.NET Core jest zgodny z [zasadami pomocy technicznej platformy .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="8a70c-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="8a70c-109">Zapoznaj się z zasadami pomocy technicznej podczas określania, które ASP.NET wersje core do obsługi w bibliotece.</span><span class="sxs-lookup"><span data-stu-id="8a70c-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="8a70c-110">Biblioteka powinna:</span><span class="sxs-lookup"><span data-stu-id="8a70c-110">A library should:</span></span>

* <span data-ttu-id="8a70c-111">Dołożyj starań, aby wspierać wszystkie ASP.NET wersje Core sklasyfikowane jako *obsługa długoterminowa* (LTS).</span><span class="sxs-lookup"><span data-stu-id="8a70c-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="8a70c-112">Nie czują się zobowiązani do obsługi ASP.NET wersji Core sklasyfikowanych jako *End of Life* (EOL).</span><span class="sxs-lookup"><span data-stu-id="8a70c-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="8a70c-113">W wersji zapoznawczej ASP.NET Core są udostępniane, zmiany podziału są księgowane w repozytorium [Aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub.</span><span class="sxs-lookup"><span data-stu-id="8a70c-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="8a70c-114">Testowanie zgodności bibliotek mogą być prowadzone w miarę opracowywania funkcji struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="8a70c-115">Korzystanie z udostępnionej struktury ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a70c-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8a70c-116">Wraz z wydaniem platformy .NET Core 3.0 wiele ASP.NET zestawów core nie są już publikowane do NuGet jako pakiety.</span><span class="sxs-lookup"><span data-stu-id="8a70c-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="8a70c-117">Zamiast tego zestawy są zawarte w `Microsoft.AspNetCore.App` udostępnionej ramach, która jest instalowana z instalatorami .NET Core SDK i runtime.</span><span class="sxs-lookup"><span data-stu-id="8a70c-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="8a70c-118">Aby uzyskać listę pakietów, które nie są już publikowane, zobacz [Usuwanie przestarzałych odwołań do pakietów](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="8a70c-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="8a70c-119">Od .NET Core 3.0 projekty `Microsoft.NET.Sdk.Web` przy użyciu SDK MSBuild niejawnie odwołują się do udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="8a70c-120">Projekty przy `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` użyciu lub SDK musi odwoływać się ASP.NET Core do korzystania ASP.NET podstawowych interfejsów API w udostępnionej ramach.</span><span class="sxs-lookup"><span data-stu-id="8a70c-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="8a70c-121">Aby odwołać się ASP.NET Core, `<FrameworkReference>` dodaj do pliku projektu następujący element:</span><span class="sxs-lookup"><span data-stu-id="8a70c-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="8a70c-122">Odwoływanie się ASP.NET Core w ten sposób jest obsługiwane tylko dla projektów przeznaczonych dla platformy .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="8a70c-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="8a70c-123">Uwzględnij rozszerzalność blazora</span><span class="sxs-lookup"><span data-stu-id="8a70c-123">Include Blazor extensibility</span></span>

<span data-ttu-id="8a70c-124">Blazor obsługuje [modele hostingu](xref:blazor/hosting-models)WebAssembly (WASM) i Server.</span><span class="sxs-lookup"><span data-stu-id="8a70c-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="8a70c-125">Jeśli nie ma konkretnego powodu, aby nie, biblioteka [składników Razor](xref:blazor/components) powinna obsługiwać oba modele hostingu.</span><span class="sxs-lookup"><span data-stu-id="8a70c-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="8a70c-126">Biblioteka składników Razor musi używać pakietu [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="8a70c-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="8a70c-127">Obsługa obu modeli hostingu</span><span class="sxs-lookup"><span data-stu-id="8a70c-127">Support both hosting models</span></span>

<span data-ttu-id="8a70c-128">Aby obsługiwać zużycie komponentów Razor zarówno z projektów [Blazor Server,](xref:blazor/hosting-models#blazor-server) jak i [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) skorzystaj z poniższych instrukcji dla edytora.</span><span class="sxs-lookup"><span data-stu-id="8a70c-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8a70c-129">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a70c-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8a70c-130">Użyj szablonu projektu **Biblioteka klas Razor.**</span><span class="sxs-lookup"><span data-stu-id="8a70c-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="8a70c-131">Pole wyboru **Strony pomocy technicznej i widoki** szablonu powinny zostać odznaczone.</span><span class="sxs-lookup"><span data-stu-id="8a70c-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8a70c-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a70c-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8a70c-133">Uruchom następujące polecenie w zintegrowanym terminalu:</span><span class="sxs-lookup"><span data-stu-id="8a70c-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8a70c-134">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8a70c-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8a70c-135">Użyj szablonu projektu **Biblioteka klas Razor.**</span><span class="sxs-lookup"><span data-stu-id="8a70c-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="8a70c-136">Projekt wygenerowany na podstawie szablonu wykonuje następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="8a70c-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="8a70c-137">Obiekty docelowe .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="8a70c-138">Ustawia `RazorLangVersion` właściwość `3.0`na .</span><span class="sxs-lookup"><span data-stu-id="8a70c-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="8a70c-139">`3.0`jest wartością domyślną dla platformy .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="8a70c-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="8a70c-140">Dodaje następujące odwołania do pakietu:</span><span class="sxs-lookup"><span data-stu-id="8a70c-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="8a70c-141">Składniki Microsoft.AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="8a70c-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="8a70c-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="8a70c-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="8a70c-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="8a70c-144">Obsługa określonego modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="8a70c-144">Support a specific hosting model</span></span>

<span data-ttu-id="8a70c-145">Znacznie rzadziej obsługuje jeden model hostingowy Blazor.</span><span class="sxs-lookup"><span data-stu-id="8a70c-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="8a70c-146">Na przykład do obsługi zużycia komponentów Razor z [blazor server](xref:blazor/hosting-models#blazor-server) projektów tylko:</span><span class="sxs-lookup"><span data-stu-id="8a70c-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="8a70c-147">Docelowy .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="8a70c-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="8a70c-148">Dodaj `<FrameworkReference>` element dla udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="8a70c-149">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="8a70c-150">Aby uzyskać więcej informacji na temat bibliotek zawierających składniki razor, zobacz [ASP.NET Biblioteki klas podstawowych składników razor](xref:blazor/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="8a70c-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="8a70c-151">Uwzględnij rozszerzalność MVC</span><span class="sxs-lookup"><span data-stu-id="8a70c-151">Include MVC extensibility</span></span>

<span data-ttu-id="8a70c-152">W tej sekcji przedstawiono zalecenia dotyczące bibliotek, które obejmują:</span><span class="sxs-lookup"><span data-stu-id="8a70c-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="8a70c-153">Widoki maszynki do golenia lub strony maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="8a70c-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="8a70c-154">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="8a70c-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="8a70c-155">Składniki widoków</span><span class="sxs-lookup"><span data-stu-id="8a70c-155">View components</span></span>](#view-components)

<span data-ttu-id="8a70c-156">W tej sekcji nie omówiono multi-targeting do obsługi wielu wersji MVC.</span><span class="sxs-lookup"><span data-stu-id="8a70c-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="8a70c-157">Aby uzyskać wskazówki dotyczące obsługi wielu wersji ASP.NET Core, zobacz [Obsługa wielu ASP.NET wersji core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="8a70c-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="8a70c-158">Widoki maszynki do golenia lub strony maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="8a70c-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="8a70c-159">Projekt zawierający [widoki Razor](xref:mvc/views/overview) lub [strony Razor](xref:razor-pages/index) musi używać [sdk Microsoft.NET.Sdk.Razor.](xref:razor-pages/sdk)</span><span class="sxs-lookup"><span data-stu-id="8a70c-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="8a70c-160">Jeśli projekt jest przeznaczony dla .NET Core 3.x, wymaga:</span><span class="sxs-lookup"><span data-stu-id="8a70c-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="8a70c-161">Właściwość `AddRazorSupportForMvc` MSBuild `true`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="8a70c-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="8a70c-162">Element `<FrameworkReference>` dla wspólnej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="8a70c-163">Szablon projektu **Biblioteka klas Razor** spełnia poprzednie wymagania dotyczące projektów przeznaczonych dla platformy .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="8a70c-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="8a70c-164">Użyj poniższych instrukcji dla edytora.</span><span class="sxs-lookup"><span data-stu-id="8a70c-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8a70c-165">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a70c-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8a70c-166">Użyj szablonu projektu **Biblioteka klas Razor.**</span><span class="sxs-lookup"><span data-stu-id="8a70c-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="8a70c-167">Należy zaznaczyć pole wyboru **Strony pomocy technicznej i widoki** szablonu.</span><span class="sxs-lookup"><span data-stu-id="8a70c-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8a70c-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a70c-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8a70c-169">Uruchom następujące polecenie w zintegrowanym terminalu:</span><span class="sxs-lookup"><span data-stu-id="8a70c-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8a70c-170">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8a70c-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8a70c-171">Brak obsługi szablonu projektu w tej chwili.</span><span class="sxs-lookup"><span data-stu-id="8a70c-171">No project template support at this time.</span></span>

---

<span data-ttu-id="8a70c-172">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="8a70c-173">Jeśli projekt jest przeznaczony dla platformy .NET Standard, wymagane jest odwołanie do pakietu [Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc)</span><span class="sxs-lookup"><span data-stu-id="8a70c-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="8a70c-174">Pakiet `Microsoft.AspNetCore.Mvc` został przeniesiony do udostępnionej struktury w ASP.NET Core 3.0 i dlatego nie jest już publikowany.</span><span class="sxs-lookup"><span data-stu-id="8a70c-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="8a70c-175">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="8a70c-176">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="8a70c-176">Tag Helpers</span></span>

<span data-ttu-id="8a70c-177">Projekt, który zawiera [Pomocników tagów](xref:mvc/views/tag-helpers/intro) należy użyć zestawu `Microsoft.NET.Sdk` SDK.</span><span class="sxs-lookup"><span data-stu-id="8a70c-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="8a70c-178">Jeśli kierowanie .NET Core 3.x, dodać `<FrameworkReference>` element dla udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="8a70c-179">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="8a70c-180">Jeśli targetowanie .NET Standard (do obsługi wersji wcześniejszych niż ASP.NET Core 3.x), należy dodać odwołanie do pakietu [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="8a70c-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="8a70c-181">Pakiet `Microsoft.AspNetCore.Mvc.Razor` został przeniesiony do wspólnej struktury i dlatego nie jest już publikowany.</span><span class="sxs-lookup"><span data-stu-id="8a70c-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="8a70c-182">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="8a70c-183">Składniki widoku</span><span class="sxs-lookup"><span data-stu-id="8a70c-183">View components</span></span>

<span data-ttu-id="8a70c-184">Projekt, który zawiera [składniki](xref:mvc/views/view-components) `Microsoft.NET.Sdk` widoku należy użyć zestawu SDK.</span><span class="sxs-lookup"><span data-stu-id="8a70c-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="8a70c-185">Jeśli kierowanie .NET Core 3.x, dodać `<FrameworkReference>` element dla udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="8a70c-186">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="8a70c-187">Jeśli targetowanie .NET Standard (do obsługi wersji wcześniejszych niż ASP.NET Core 3.x), dodaj odwołanie do pakietu [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="8a70c-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="8a70c-188">Pakiet `Microsoft.AspNetCore.Mvc.ViewFeatures` został przeniesiony do wspólnej struktury i dlatego nie jest już publikowany.</span><span class="sxs-lookup"><span data-stu-id="8a70c-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="8a70c-189">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="8a70c-190">Obsługa wielu wersji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a70c-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="8a70c-191">Multi-targeting jest wymagane do tworzenia biblioteki, która obsługuje wiele wariantów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a70c-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="8a70c-192">Rozważmy scenariusz, w którym biblioteka Pomocników tagów musi obsługiwać następujące ASP.NET warianty rdzenia:</span><span class="sxs-lookup"><span data-stu-id="8a70c-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="8a70c-193">ASP.NET Core 2.1 ukierunkowany na .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="8a70c-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="8a70c-194">ASP.NET Core 2.x kierowania .NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="8a70c-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="8a70c-195">ASP.NET Core 3.x kierowania .NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="8a70c-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="8a70c-196">Następujący plik projektu obsługuje te `TargetFrameworks` warianty za pośrednictwem właściwości:</span><span class="sxs-lookup"><span data-stu-id="8a70c-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="8a70c-197">W poprzednim pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="8a70c-197">With the preceding project file:</span></span>

* <span data-ttu-id="8a70c-198">Pakiet `Markdig` jest dodawany dla wszystkich konsumentów.</span><span class="sxs-lookup"><span data-stu-id="8a70c-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="8a70c-199">Odwołanie do [pliku Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) jest dodawane dla konsumentów przeznaczonych dla platformy .NET Framework 4.6.1 lub nowszych lub .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="8a70c-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="8a70c-200">Wersja 2.1.0 pakietu współpracuje z ASP.NET Core 2.2 ze względu na zgodność z powrotem.</span><span class="sxs-lookup"><span data-stu-id="8a70c-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="8a70c-201">Do udostępnionej struktury odwołuje się do konsumentów kierowanych na .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="8a70c-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="8a70c-202">Pakiet `Microsoft.AspNetCore.Mvc.Razor` znajduje się w udostępnionej ramach.</span><span class="sxs-lookup"><span data-stu-id="8a70c-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="8a70c-203">Alternatywnie można kierować na program .NET Standard 2.0 zamiast kierować reklamy na program .NET Core 2.1 i .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="8a70c-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="8a70c-204">W poprzednim pliku projektu istnieją następujące zastrzeżenia:</span><span class="sxs-lookup"><span data-stu-id="8a70c-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="8a70c-205">Ponieważ biblioteka zawiera tylko Pomocników tagów, jest bardziej proste do kierowania określonych platform, na których działa ASP.NET Core: .NET Core i .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="8a70c-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="8a70c-206">Pomocników tagów nie może być używany przez inne platformy docelowe zgodne ze standardem .NET Standard 2.0, takie jak Unity, UWP i Xamarin.</span><span class="sxs-lookup"><span data-stu-id="8a70c-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="8a70c-207">Za pomocą .NET Standard 2.0 z .NET Framework ma pewne problemy, które zostały rozwiązane w .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="8a70c-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="8a70c-208">Można poprawić środowisko dla konsumentów korzystających z programu .NET Framework 4.6.1 do 4.7.1, kierując na .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="8a70c-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="8a70c-209">Jeśli biblioteka musi wywoływać interfejsy API specyficzne dla platformy, należy kierować określone implementacje platformy .NET zamiast .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="8a70c-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="8a70c-210">Aby uzyskać więcej informacji, zobacz [Kierowanie zbiorcze](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="8a70c-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="8a70c-211">Używanie interfejsu API, który się nie zmienił</span><span class="sxs-lookup"><span data-stu-id="8a70c-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="8a70c-212">Wyobraź sobie scenariusz, w którym uaktualniasz bibliotekę oprogramowania pośredniczącego z platformy .NET Core 2.2 do 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="8a70c-213">Interfejsy API oprogramowania pośredniczącego ASP.NET Core używane w bibliotece nie uległy zmianie między ASP.NET Core 2.2 i 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="8a70c-214">Aby kontynuować obsługę biblioteki oprogramowania pośredniczącego w .NET Core 3.0, należy wykonać następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="8a70c-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="8a70c-215">Postępuj zgodnie [ze standardowymi wskazówkami dotyczącymi biblioteki](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="8a70c-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="8a70c-216">Dodaj odwołanie do pakietu dla każdego pakietu NuGet interfejsu API, jeśli odpowiedni zestaw nie istnieje w udostępnionej ramach.</span><span class="sxs-lookup"><span data-stu-id="8a70c-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="8a70c-217">Używanie interfejsu API, który uległ zmianie</span><span class="sxs-lookup"><span data-stu-id="8a70c-217">Use an API that changed</span></span>

<span data-ttu-id="8a70c-218">Wyobraź sobie scenariusz, w którym uaktualniasz bibliotekę z platformy .NET Core 2.2 do .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="8a70c-219">Interfejs API ASP.NET Core używany w bibliotece ma [przełomową zmianę](/dotnet/core/compatibility/breaking-changes) w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a70c-220">Należy wziąć pod uwagę, czy biblioteki można przepisać, aby nie używać uszkodzonego interfejsu API we wszystkich wersjach.</span><span class="sxs-lookup"><span data-stu-id="8a70c-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="8a70c-221">Jeśli można przepisać biblioteki, to zrobić i nadal kierować wcześniej platformę docelową (na przykład .NET Standard 2.0 lub .NET Framework 4.6.1) z odwołaniami do pakietu.</span><span class="sxs-lookup"><span data-stu-id="8a70c-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="8a70c-222">Jeśli nie można przepisać biblioteki, wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="8a70c-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="8a70c-223">Dodaj obiekt docelowy dla platformy .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="8a70c-224">Dodaj `<FrameworkReference>` element dla udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="8a70c-225">Użyj [dyrektywy preprocesora #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) z odpowiednim symbolem struktury docelowej, aby warunkowo skompilować kod.</span><span class="sxs-lookup"><span data-stu-id="8a70c-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="8a70c-226">Na przykład synchroniczne odczyty i zapisy w strumieniach żądań i odpowiedzi HTTP są domyślnie wyłączone od ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a70c-227">ASP.NET Core 2.2 domyślnie obsługuje zachowanie synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="8a70c-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="8a70c-228">Należy wziąć pod uwagę biblioteki oprogramowania pośredniczącego, w którym synchroniczne odczyty i zapisy powinny być włączone, gdzie występują we/wy.</span><span class="sxs-lookup"><span data-stu-id="8a70c-228">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="8a70c-229">Biblioteka należy dołączyć kod, aby włączyć funkcje synchroniczne w odpowiedniej dyrektywy preprocesora.</span><span class="sxs-lookup"><span data-stu-id="8a70c-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="8a70c-230">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8a70c-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="8a70c-231">Użyj interfejsu API wprowadzonego w 3.0</span><span class="sxs-lookup"><span data-stu-id="8a70c-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="8a70c-232">Wyobraź sobie, że chcesz użyć ASP.NET core API, który został wprowadzony w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a70c-233">Zastanów się nad następującymi pytaniami:</span><span class="sxs-lookup"><span data-stu-id="8a70c-233">Consider the following questions:</span></span>

1. <span data-ttu-id="8a70c-234">Czy biblioteka funkcjonalnie wymaga nowego interfejsu API?</span><span class="sxs-lookup"><span data-stu-id="8a70c-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="8a70c-235">Czy biblioteka może zaimplementować tę funkcję w inny sposób?</span><span class="sxs-lookup"><span data-stu-id="8a70c-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="8a70c-236">Jeśli biblioteka funkcjonalnie wymaga interfejsu API i nie ma sposobu, aby zaimplementować go w dół:</span><span class="sxs-lookup"><span data-stu-id="8a70c-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="8a70c-237">Tylko docelowy .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="8a70c-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="8a70c-238">Dodaj `<FrameworkReference>` element dla udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="8a70c-239">Jeśli biblioteka może zaimplementować operację w inny sposób:</span><span class="sxs-lookup"><span data-stu-id="8a70c-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="8a70c-240">Dodaj .NET Core 3.x jako platformę docelową.</span><span class="sxs-lookup"><span data-stu-id="8a70c-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="8a70c-241">Dodaj `<FrameworkReference>` element dla udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="8a70c-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="8a70c-242">Użyj [dyrektywy preprocesora #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) z odpowiednim symbolem struktury docelowej, aby warunkowo skompilować kod.</span><span class="sxs-lookup"><span data-stu-id="8a70c-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="8a70c-243">Na przykład następujące Pomocnik tagów <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> używa interfejsu wprowadzonego w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="8a70c-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a70c-244">Konsumenci kierowani na program .NET Core 3.0 `NETCOREAPP3_0` wykonują ścieżkę kodu zdefiniowaną przez symbol struktury docelowej.</span><span class="sxs-lookup"><span data-stu-id="8a70c-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="8a70c-245">Typ parametru konstruktora pomocnika <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> tagu zmienia się na dla konsumentów .NET Core 2.1 i .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="8a70c-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="8a70c-246">Ta zmiana była konieczna, ponieważ ASP.NET Core `IHostingEnvironment` 3.0 oznaczone jako przestarzałe i zalecane `IWebHostEnvironment` jako zamiennik.</span><span class="sxs-lookup"><span data-stu-id="8a70c-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="8a70c-247">Następujący plik projektu z wieloma celami obsługuje ten scenariusz Pomocnika znaczników:</span><span class="sxs-lookup"><span data-stu-id="8a70c-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="8a70c-248">Używanie interfejsu API usuniętego z udostępnionej struktury</span><span class="sxs-lookup"><span data-stu-id="8a70c-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="8a70c-249">Aby użyć zestawu ASP.NET Core, który został usunięty z udostępnionej struktury, dodaj odpowiednie odwołanie do pakietu.</span><span class="sxs-lookup"><span data-stu-id="8a70c-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="8a70c-250">Aby uzyskać listę pakietów usuniętych z udostępnionej struktury w ASP.NET Core 3.0, zobacz [Usuwanie przestarzałych odwołań do pakietów](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="8a70c-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="8a70c-251">Na przykład, aby dodać klienta interfejsu API sieci Web:</span><span class="sxs-lookup"><span data-stu-id="8a70c-251">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="8a70c-252">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8a70c-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="8a70c-253">Pomoc techniczna w zakresie implementacji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="8a70c-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="8a70c-254">Zasady pomocy technicznej platformy .NET</span><span class="sxs-lookup"><span data-stu-id="8a70c-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
