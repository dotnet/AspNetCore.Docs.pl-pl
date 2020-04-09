---
title: Microsoft.AspNetCore.App metapackage dla ASP.NET Core
author: Rick-Anderson
description: Struktura współużytkowana Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511382"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="0cc5b-103">Microsoft.AspNetCore.App dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cc5b-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="0cc5b-104">Wspólna struktura ASP.NET Core`Microsoft.AspNetCore.App`( ) zawiera zestawy, które są opracowywane i obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="0cc5b-105">`Microsoft.AspNetCore.App`jest instalowany po zainstalowaniu [pliku .NET Core 3.0 lub nowszego SDK.](https://dotnet.microsoft.com/download/dotnet-core/3.0)</span><span class="sxs-lookup"><span data-stu-id="0cc5b-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="0cc5b-106">*Współużytkowana struktura* to zestaw zestawów (plików*dll),* które są zainstalowane na komputerze i zawierają składnik środowiska wykonawczego i pakiet docelowy.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="0cc5b-107">Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="0cc5b-108">Projekty, które `Microsoft.NET.Sdk.Web` są przeznaczone dla `Microsoft.AspNetCore.App` SDK niejawnie odwołać się do struktury.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="0cc5b-109">W przypadku tych projektów nie są wymagane żadne dodatkowe odniesienia:</span><span class="sxs-lookup"><span data-stu-id="0cc5b-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="0cc5b-110">Współdzielone ramy ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc5b-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="0cc5b-111">Nie obejmuje zależności innych firm.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="0cc5b-112">Zawiera wszystkie obsługiwane pakiety przez zespół ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0cc5b-113">Ta funkcja wymaga ASP.NET celu Core 2.x .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="0cc5b-114">[Metapakiet](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc5b-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="0cc5b-115">Nie obejmuje zależności innych firm z wyjątkiem [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/)i [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="0cc5b-116">Te zależności innych firm są uważane za niezbędne do zapewnienia funkcji głównych struktur.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="0cc5b-117">Zawiera wszystkie obsługiwane pakiety przez zespół ASP.NET Core, z wyjątkiem tych, które zawierają zależności innych firm (inne niż wcześniej wymienione).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="0cc5b-118">Zawiera wszystkie obsługiwane pakiety przez zespół Entity Framework Core z wyjątkiem tych, które zawierają zależności innych firm (inne niż wcześniej wymienione).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="0cc5b-119">Wszystkie funkcje ASP.NET Core 2.x i Entity Framework Core 2.x `Microsoft.AspNetCore.App` są zawarte w pakiecie.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="0cc5b-120">Domyślne szablony projektów przeznaczone dla ASP.NET Core 2.x używają tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="0cc5b-121">Zalecamy aplikacje przeznaczone ASP.NET Core 2.x i Entity Framework Core `Microsoft.AspNetCore.App` 2.x używać pakietu.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="0cc5b-122">Numer wersji `Microsoft.AspNetCore.App` metapakietu reprezentuje minimalną ASP.NET wersję Core i wersję Core programu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="0cc5b-123">Korzystanie `Microsoft.AspNetCore.App` z metapakiety zapewnia ograniczenia wersji, które chronią aplikację:</span><span class="sxs-lookup"><span data-stu-id="0cc5b-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="0cc5b-124">Jeśli pakiet jest dołączony, który ma przechodnie (nie bezpośrednie) `Microsoft.AspNetCore.App`zależność od pakietu w , a te numery wersji różnią się, NuGet wygeneruje błąd.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="0cc5b-125">Inne pakiety dodane do aplikacji nie mogą `Microsoft.AspNetCore.App`zmieniać wersji pakietów zawartych w pliku .</span><span class="sxs-lookup"><span data-stu-id="0cc5b-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="0cc5b-126">Spójność wersji zapewnia niezawodne środowisko.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="0cc5b-127">`Microsoft.AspNetCore.App`został zaprojektowany, aby zapobiec nieprzetestowanych kombinacji wersji powiązanych bitów używanych razem w tej samej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="0cc5b-128">Aplikacje korzystające `Microsoft.AspNetCore.App` z metapakiety automatycznie korzystają z udostępnionej struktury ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="0cc5b-129">Korzystając z `Microsoft.AspNetCore.App` metapakietu, **żadne** zasoby z pakietów Core NuGet, do których&mdash;odwołuje się odwołuje się ASP.NET Core NuGet, nie są wdrażane z aplikacją, ASP.NET framework udostępniony core zawiera te zasoby.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="0cc5b-130">Zasoby w udostępnionej ramach są wstępnie kompilowane w celu zwiększenia czasu uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="0cc5b-131">Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="0cc5b-132">Następujący plik projektu odwołuje `Microsoft.AspNetCore.App` się do metapakiety dla ASP.NET Core i reprezentuje typowy szablon ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="0cc5b-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="0cc5b-133">Poprzedni znacznik reprezentuje typowy szablon ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="0cc5b-134">Nie określa numeru wersji dla `Microsoft.AspNetCore.App` odwołania do pakietu.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="0cc5b-135">Gdy wersja nie jest określona, wersja [niejawna](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) jest określona przez SDK, czyli `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="0cc5b-136">Zaleca się poleganie na wersji niejawnej określonej przez SDK i nie jawnie ustawienie numeru wersji na odwołanie do pakietu.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="0cc5b-137">Jeśli masz pytania dotyczące tego podejścia, zostaw komentarz GitHub w [dyskusji dla microsoft.AspNetCore.App niejawna wersja](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="0cc5b-138">Wersja niejawna `major.minor.0` jest ustawiona na aplikacje przenośne.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="0cc5b-139">Mechanizm przekazywania udostępnionej struktury będzie uruchamiał aplikację w najnowszej zgodnej wersji wśród zainstalowanych udostępnionych struktur.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="0cc5b-140">Aby zagwarantować, że ta sama wersja jest używana w programach, testach i produkcji, upewnij się, że ta sama wersja udostępnionej struktury jest zainstalowana we wszystkich środowiskach.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="0cc5b-141">W przypadku aplikacji zawierających samą siebie niejawny numer wersji jest ustawiony na `major.minor.patch` współużytkowane framework w pakiecie z zainstalowanym zestawem SDK.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="0cc5b-142">Określenie numeru wersji w `Microsoft.AspNetCore.App` odwołaniu **nie** gwarantuje, że zostanie wybrana wersja udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="0cc5b-143">Załóżmy na przykład, że określono wersję "2.2.1", ale jest zainstalowana "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="0cc5b-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="0cc5b-144">W takim przypadku aplikacja użyje "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="0cc5b-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="0cc5b-145">Chociaż nie jest to zalecane, można wyłączyć roll forward (łatka i/lub drobne).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="0cc5b-146">Aby uzyskać więcej informacji dotyczących roll-forward hosta dotnet i sposobu konfigurowania jego zachowania, zobacz [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="0cc5b-147">`<Project Sdk`musi być `Microsoft.NET.Sdk.Web` ustawiona na `Microsoft.AspNetCore.App`użycie wersji niejawnej .</span><span class="sxs-lookup"><span data-stu-id="0cc5b-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="0cc5b-148">Gdy `<Project Sdk="Microsoft.NET.Sdk">` (bez spływu) stosuje się: `.Web`</span><span class="sxs-lookup"><span data-stu-id="0cc5b-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="0cc5b-149">Generowane jest następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="0cc5b-149">The following warning is generated:</span></span>

  <span data-ttu-id="0cc5b-150">*Ostrzeżenie NU1604: Zależność projektu Microsoft.AspNetCore.App nie zawiera włącznie dolnej granicy. Dołącz dolną granicę w wersji zależności, aby zapewnić spójne wyniki przywracania.*</span><span class="sxs-lookup"><span data-stu-id="0cc5b-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="0cc5b-151">Jest to znany problem z sdk .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="0cc5b-152">Aktualizacja ASP.NET rdzenia</span><span class="sxs-lookup"><span data-stu-id="0cc5b-152">Update ASP.NET Core</span></span>

<span data-ttu-id="0cc5b-153">`Microsoft.AspNetCore.App` [Metapakiet](/dotnet/core/packages#metapackages) nie jest tradycyjnym pakietem, który jest aktualizowany z NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="0cc5b-154">Podobne `Microsoft.NETCore.App`do `Microsoft.AspNetCore.App` , reprezentuje udostępnionego środowiska uruchomieniowego, który ma specjalne semantyki wersji obsługiwane poza NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="0cc5b-155">Aby uzyskać więcej informacji, zobacz [Pakiety, metapakiety i struktury](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="0cc5b-156">Aby zaktualizować ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0cc5b-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="0cc5b-157">Na komputerach deweloperskich i budować serwery: Pobierz i zainstaluj [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="0cc5b-158">Na serwerach wdrażania: Pobierz i zainstaluj [środowisko uruchomieniowe .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="0cc5b-159">Aplikacje zostaną przeniesione do najnowszej zainstalowanej wersji po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="0cc5b-160">Nie jest konieczne aktualizowanie `Microsoft.AspNetCore.App` numeru wersji w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="0cc5b-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="0cc5b-161">Aby uzyskać więcej informacji, zobacz [Aplikacje zależne od struktury są przewijane do przodu](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="0cc5b-162">Jeśli aplikacja była `Microsoft.AspNetCore.All`wcześniej używana , zobacz [Migrowanie z pliku Microsoft.AspNetCore.All do pliku Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="0cc5b-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
