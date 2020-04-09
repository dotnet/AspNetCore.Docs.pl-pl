---
title: Microsoft.AspNetCore.All metapakiet dla ASP.NET Core 2.0
author: Rick-Anderson
description: Metapakiet Microsoft.AspNetCore.All nie jest zalecany dla ASP.NET Core 2.1 i nowszych.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: cc00c075909da5c17a4aa2fd252c9e662e5a0fc9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511070"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="5bbd6-103">Microsoft.AspNetCore.All metapakiet dla ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="5bbd6-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bbd6-104">Metapakiet `Microsoft.AspNetCore.All` nie jest zawarty w ASP.NET Core 3.0 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="5bbd6-105">Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/aspnet/Announcements/issues/314).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5bbd6-106">Zalecamy aplikacje przeznaczone ASP.NET Core 2.1 i później użyć [microsoft.AspNetCore.App metapakiet,](xref:fundamentals/metapackage-app) a nie tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="5bbd6-107">Zobacz [Migrowanie z pliku Microsoft.AspNetCore.All do pliku Microsoft.AspNetCore.App](#migrate) w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="5bbd6-108">Ta funkcja wymaga ASP.NET celu Core 2.x .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="5bbd6-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) jest metapakiet, który odnosi się do udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="5bbd6-110">*Współużytkowana struktura* to zestaw zestawów (plików*dll),* które nie znajdują się w folderach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="5bbd6-111">Aby uruchomić aplikację, należy zainstalować platformę współużytkową.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="5bbd6-112">Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="5bbd6-113">Współużytkowane ramy, które `Microsoft.AspNetCore.All` się odwołują, obejmują:</span><span class="sxs-lookup"><span data-stu-id="5bbd6-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="5bbd6-114">Wszystkie obsługiwane pakiety przez zespół ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="5bbd6-115">Wszystkie obsługiwane pakiety przez entity framework core.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="5bbd6-116">Wewnętrzne i zewnętrzne zależności używane przez ASP.NET Core i Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="5bbd6-117">Wszystkie funkcje ASP.NET Core 2.x i Entity Framework Core 2.x `Microsoft.AspNetCore.All` są zawarte w pakiecie.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="5bbd6-118">Domyślne szablony projektów przeznaczone dla ASP.NET Core 2.0 używają tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="5bbd6-119">Numer wersji `Microsoft.AspNetCore.All` metapakietu reprezentuje minimalną ASP.NET wersję Core i wersję Core programu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="5bbd6-120">Następujący plik *csproj* odwołuje `Microsoft.AspNetCore.All` się do metapakiety dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5bbd6-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="5bbd6-121">Niejawne przechowywanie wersji</span><span class="sxs-lookup"><span data-stu-id="5bbd6-121">Implicit versioning</span></span>

<span data-ttu-id="5bbd6-122">W ASP.NET Core 2.1 lub nowszym `Microsoft.AspNetCore.All` można określić odwołanie do pakietu bez wersji.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="5bbd6-123">Gdy wersja nie jest określona, wersja niejawna jest`Microsoft.NET.Sdk.Web`określona przez SDK ( ).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="5bbd6-124">Zaleca się poleganie na wersji niejawnej określonej przez SDK i nie jawnie ustawienie numeru wersji na odwołanie do pakietu.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="5bbd6-125">Jeśli masz pytania dotyczące tego podejścia, pozostaw komentarz gitHub w [dyskusji dla wersji niejawnej Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="5bbd6-126">Wersja niejawna `major.minor.0` jest ustawiona na aplikacje przenośne.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="5bbd6-127">Mechanizm przekazywania udostępnionej struktury uruchamia aplikację w najnowszej zgodnej wersji wśród zainstalowanych udostępnionych struktur.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="5bbd6-128">Aby zagwarantować, że ta sama wersja jest używana w programach, testach i produkcji, upewnij się, że ta sama wersja udostępnionej struktury jest zainstalowana we wszystkich środowiskach.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="5bbd6-129">W przypadku aplikacji samodzielnych numer wersji niejawnej jest ustawiany na `major.minor.patch` platformę udostępnionej w pakiecie z zainstalowanym zestawem SDK.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="5bbd6-130">Określenie numeru wersji w `Microsoft.AspNetCore.All` odwołaniu do pakietu **nie** gwarantuje, że wybrano wersję udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="5bbd6-131">Załóżmy na przykład, że określono wersję "2.1.1", ale jest zainstalowana "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="5bbd6-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="5bbd6-132">W takim przypadku aplikacja użyje "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="5bbd6-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="5bbd6-133">Chociaż nie jest to zalecane, można wyłączyć roll forward (łatka i/lub drobne).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="5bbd6-134">Aby uzyskać więcej informacji dotyczących roll-forward hosta dotnet i sposobu konfigurowania jego zachowania, zobacz [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="5bbd6-135">Zestaw SDK projektu musi być `Microsoft.NET.Sdk.Web` ustawiony w pliku projektu, `Microsoft.AspNetCore.All`aby użyć niejawnej wersji pliku .</span><span class="sxs-lookup"><span data-stu-id="5bbd6-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="5bbd6-136">Po `Microsoft.NET.Sdk` określeniu SDK (w`<Project Sdk="Microsoft.NET.Sdk">` górnej części pliku projektu) generowane jest następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="5bbd6-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="5bbd6-137">*Ostrzeżenie NU1604: Zależność projektu Microsoft.AspNetCore.All nie zawiera włącznie dolnej granicy. Dołącz dolną granicę w wersji zależności, aby zapewnić spójne wyniki przywracania.*</span><span class="sxs-lookup"><span data-stu-id="5bbd6-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="5bbd6-138">Jest to znany problem z .NET Core 2.1 SDK i zostanie rozwiązany w .NET Core 2.2 SDK.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="5bbd6-139">Migracja z pliku Microsoft.AspNetCore.All do pliku Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="5bbd6-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="5bbd6-140">Następujące pakiety są `Microsoft.AspNetCore.All` zawarte w `Microsoft.AspNetCore.App` pakiecie, ale nie.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

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

<span data-ttu-id="5bbd6-141">Aby przejść `Microsoft.AspNetCore.All` `Microsoft.AspNetCore.App`z do , jeśli aplikacja używa żadnych interfejsów API z powyższych pakietów lub pakietów wniesionych przez te pakiety, dodaj odwołania do tych pakietów w projekcie.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="5bbd6-142">Wszelkie zależności poprzednich pakietów, które w przeciwnym `Microsoft.AspNetCore.App` razie nie są zależności nie są uwzględniane niejawnie.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="5bbd6-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5bbd6-143">For example:</span></span>

* <span data-ttu-id="5bbd6-144">`StackExchange.Redis`jako zależność od`Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="5bbd6-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="5bbd6-145">`Microsoft.ApplicationInsights`jako zależność od`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5bbd6-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="5bbd6-146">Aktualizacja ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="5bbd6-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="5bbd6-147">Zalecamy migrację `Microsoft.AspNetCore.App` do metapakiety dla 2.1 i nowsze.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="5bbd6-148">Aby nadal `Microsoft.AspNetCore.All` korzystać z metapakiety i upewnić się, że najnowsza wersja poprawki jest wdrożona:</span><span class="sxs-lookup"><span data-stu-id="5bbd6-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="5bbd6-149">Na komputerach deweloperskich i serwerach kompilacji: Zainstaluj najnowszy [pakiet .NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="5bbd6-150">Na serwerach wdrażania: Zainstaluj najnowsze [środowisko uruchomieniowe .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5bbd6-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="5bbd6-151">Aplikacja zostanie przerzuci do najnowszej zainstalowanej wersji po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5bbd6-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
