---
title: Microsoft. AspNetCore. All, pakiet dla ASP.NET Core 2,0
author: Rick-Anderson
description: Nie zaleca się stosowania pakietu Microsoft. AspNetCore. All w przypadku ASP.NET Core 2,1 i nowszych.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/metapackage
ms.openlocfilehash: 58201aed604fa53c703176e11af5d749f2b3274e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017037"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="e4f04-103">Microsoft. AspNetCore. All, pakiet dla ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="e4f04-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e4f04-104">`Microsoft.AspNetCore.All`Pakiet nie znajduje się w ASP.NET Core 3,0 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="e4f04-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="e4f04-105">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/aspnet/Announcements/issues/314)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="e4f04-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e4f04-106">Zalecamy stosowanie aplikacji przeznaczonych dla ASP.NET Core 2,1 i nowszych przy użyciu [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a nie tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="e4f04-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="e4f04-107">Zobacz [Migrowanie z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](#migrate) w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="e4f04-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="e4f04-108">Ta funkcja wymaga ASP.NET Core 2. x przeznaczonych dla platformy .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="e4f04-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="e4f04-109">[Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) to pakiet, który odwołuje się do udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="e4f04-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="e4f04-110">*Struktura udostępniona* to zbiór zestawów (plików*dll* ), które nie znajdują się w folderach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e4f04-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="e4f04-111">Aby można było uruchomić aplikację, na komputerze musi być zainstalowana struktura udostępniona.</span><span class="sxs-lookup"><span data-stu-id="e4f04-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="e4f04-112">Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="e4f04-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="e4f04-113">Współdzielona struktura, `Microsoft.AspNetCore.All` do której odwołuje się obejmuje:</span><span class="sxs-lookup"><span data-stu-id="e4f04-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="e4f04-114">Wszystkie obsługiwane pakiety przez zespół ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4f04-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="e4f04-115">Wszystkie obsługiwane pakiety przez Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e4f04-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="e4f04-116">Zależności wewnętrzne i inne firmy używane przez ASP.NET Core i Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e4f04-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="e4f04-117">Wszystkie funkcje ASP.NET Core 2. x i Entity Framework Core 2. x są zawarte w `Microsoft.AspNetCore.All` pakiecie.</span><span class="sxs-lookup"><span data-stu-id="e4f04-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="e4f04-118">Domyślne szablony projektu dla ASP.NET Core 2,0 używają tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="e4f04-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="e4f04-119">Numer wersji `Microsoft.AspNetCore.All` pakietubinding reprezentuje minimalną wersję ASP.NET Core i Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e4f04-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="e4f04-120">Następujący plik *. csproj* odwołuje się do `Microsoft.AspNetCore.All` pakietu dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e4f04-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="e4f04-121">Niejawna wersja</span><span class="sxs-lookup"><span data-stu-id="e4f04-121">Implicit versioning</span></span>

<span data-ttu-id="e4f04-122">W ASP.NET Core 2,1 lub nowszej można określić `Microsoft.AspNetCore.All` odwołanie do pakietu bez wersji.</span><span class="sxs-lookup"><span data-stu-id="e4f04-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="e4f04-123">Gdy wersja nie jest określona, zestaw SDK () nie określa wersji niejawnej `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="e4f04-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="e4f04-124">Zalecamy użycie niejawnej wersji określonej przez zestaw SDK i niejawne ustawienie numeru wersji w odwołaniu do pakietu.</span><span class="sxs-lookup"><span data-stu-id="e4f04-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="e4f04-125">Jeśli masz pytania dotyczące tego podejścia, pozostaw komentarz w serwisie GitHub w [dyskusji dotyczącej wersji niejawnej Microsoft. AspNetCore. app](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="e4f04-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="e4f04-126">Niejawna wersja jest ustawiona na `major.minor.0` dla aplikacji przenośnych.</span><span class="sxs-lookup"><span data-stu-id="e4f04-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="e4f04-127">Mechanizm przekazujący przechodzenie do platformy udostępnionej uruchamia aplikację w najnowszej zgodnej wersji wśród zainstalowanych platform udostępnionych.</span><span class="sxs-lookup"><span data-stu-id="e4f04-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="e4f04-128">Aby zagwarantować, że ta sama wersja jest używana w środowisku deweloperskim, testowym i produkcyjnym, upewnij się, że ta sama wersja udostępnionej platformy jest zainstalowana we wszystkich środowiskach.</span><span class="sxs-lookup"><span data-stu-id="e4f04-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="e4f04-129">W przypadku aplikacji samodzielnych niejawny numer wersji jest ustawiany na `major.minor.patch` współużytkowanej platformie powiązanej z zainstalowanym zestawem SDK.</span><span class="sxs-lookup"><span data-stu-id="e4f04-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="e4f04-130">Określenie numeru wersji w `Microsoft.AspNetCore.All` odwołaniu do pakietu nie **not** gwarantuje, że jest wybrana wersja udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="e4f04-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="e4f04-131">Załóżmy na przykład, że jest określona wersja "2.1.1", ale jest zainstalowana wartość "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="e4f04-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="e4f04-132">W takim przypadku aplikacja będzie używać "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="e4f04-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="e4f04-133">Chociaż nie jest to zalecane, można wyłączyć funkcję wycofywania do przodu (poprawka i/lub pomocnicza).</span><span class="sxs-lookup"><span data-stu-id="e4f04-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="e4f04-134">Aby uzyskać więcej informacji na temat przetworzenia i konfigurowania zachowań hosta dotnet, zobacz [przewinięcie hosta dotnet do przodu](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="e4f04-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="e4f04-135">Zestaw SDK projektu musi być ustawiony na `Microsoft.NET.Sdk.Web` w pliku projektu, aby można było użyć niejawnej wersji programu `Microsoft.AspNetCore.All` .</span><span class="sxs-lookup"><span data-stu-id="e4f04-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="e4f04-136">Gdy `Microsoft.NET.Sdk` zestaw SDK jest określony ( `<Project Sdk="Microsoft.NET.Sdk">` w górnej części pliku projektu), generowane jest następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="e4f04-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="e4f04-137">*Ostrzeżenie NU1604: zależność projektu Microsoft. AspNetCore. All nie zawiera mniejszej granicy. Uwzględnij dolną granicę w wersji zależności, aby zapewnić spójne wyniki przywracania.*</span><span class="sxs-lookup"><span data-stu-id="e4f04-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="e4f04-138">Jest to znany problem z zestawem SDK platformy .NET Core 2,1 i zostanie naprawiony w zestawie SDK platformy .NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="e4f04-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="e4f04-139">Migrowanie z Microsoft. AspNetCore. All do Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="e4f04-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="e4f04-140">Następujące pakiety są zawarte w `Microsoft.AspNetCore.All` pakiecie, ale nie `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="e4f04-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="e4f04-141">Aby przenieść z `Microsoft.AspNetCore.All` do `Microsoft.AspNetCore.App` , jeśli aplikacja używa dowolnych interfejsów API z powyższych pakietów lub pakietów wprowadzonych przez te pakiety, Dodaj odwołania do tych pakietów w projekcie.</span><span class="sxs-lookup"><span data-stu-id="e4f04-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="e4f04-142">Wszystkie zależności poprzedzających pakietów, które w przeciwnym razie nie są zależnościami, `Microsoft.AspNetCore.App` nie są uwzględniane niejawnie.</span><span class="sxs-lookup"><span data-stu-id="e4f04-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="e4f04-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e4f04-143">For example:</span></span>

* <span data-ttu-id="e4f04-144">`StackExchange.Redis`jako zależność`Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="e4f04-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="e4f04-145">`Microsoft.ApplicationInsights`jako zależność`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e4f04-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="e4f04-146">Aktualizacja ASP.NET Core 2,1</span><span class="sxs-lookup"><span data-stu-id="e4f04-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="e4f04-147">Zalecamy przeprowadzenie migracji do `Microsoft.AspNetCore.App` pakietu dla programu w wersji 2,1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="e4f04-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="e4f04-148">Aby nadal korzystać z `Microsoft.AspNetCore.All` pakietu i upewnić się, że jest wdrożona Najnowsza wersja poprawki:</span><span class="sxs-lookup"><span data-stu-id="e4f04-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="e4f04-149">Na komputerach deweloperskich i serwerach kompilacji: Zainstaluj najnowsze [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="e4f04-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="e4f04-150">Na serwerach wdrożenia: Zainstaluj najnowsze [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="e4f04-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="e4f04-151">Aplikacja zostanie przeniesiona do najnowszej zainstalowanej wersji przy ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e4f04-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
