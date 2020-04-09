---
title: Migracja z ASP.NET Core 1.x do 2.0
author: scottaddie
description: W tym artykule opisano wymagania wstępne i najczęstsze kroki migracji projektu ASP.NET Core 1.x w celu ASP.NET core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/1x-to-2x/index
ms.openlocfilehash: c46f50a418cf630980ac2ba94407e4370d36e7d5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667616"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="3c4c6-103">Migracja z ASP.NET Core 1.x do 2.0</span><span class="sxs-lookup"><span data-stu-id="3c4c6-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="3c4c6-104">Przez [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3c4c6-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3c4c6-105">W tym artykule prowadzimy cię przez aktualizację istniejącego projektu core 1.x ASP.NET, aby ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="3c4c6-106">Migracja aplikacji do ASP.NET Core 2.0 umożliwia korzystanie z [wielu nowych funkcji i ulepszeń wydajności.](xref:aspnetcore-2.0)</span><span class="sxs-lookup"><span data-stu-id="3c4c6-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="3c4c6-107">Istniejące aplikacje ASP.NET Core 1.x są oparte na szablonach projektów specyficznych dla wersji.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="3c4c6-108">W miarę rozwoju ASP.NET core framework, podobnie jak szablony projektów i kod startowy zawarty w nich.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="3c4c6-109">Oprócz aktualizowania ASP.NET framework Core, należy zaktualizować kod dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="3c4c6-110">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3c4c6-110">Prerequisites</span></span>

<span data-ttu-id="3c4c6-111">Zobacz [Wprowadzenie do ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="3c4c6-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="3c4c6-112">Aktualizacja moniker struktury docelowej (TFM)</span><span class="sxs-lookup"><span data-stu-id="3c4c6-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="3c4c6-113">Projekty przeznaczone dla platformy .NET Core powinny używać [TFM](/dotnet/standard/frameworks) wersji większej lub równej .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="3c4c6-114">Wyszukaj `<TargetFramework>` węzeł w pliku *csproj* i zastąp jego wewnętrzny `netcoreapp2.0`tekst:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="3c4c6-115">Projekty przeznaczone dla platformy .NET Framework należy użyć TFM wersji większej lub równej .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="3c4c6-116">Wyszukaj `<TargetFramework>` węzeł w pliku *csproj* i zastąp jego wewnętrzny `net461`tekst:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="3c4c6-117">Program .NET Core 2.0 oferuje znacznie większą powierzchnię niż .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="3c4c6-118">Jeśli kierujesz reklamy do platformy .NET Framework wyłącznie z powodu brakujących interfejsów API w systemie .NET Core 1.x, kierowanie na program .NET Core 2.0 prawdopodobnie zadziała.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="3c4c6-119">Jeśli plik projektu `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`zawiera , zobacz [ten problem GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span><span class="sxs-lookup"><span data-stu-id="3c4c6-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="3c4c6-120">Aktualizowanie wersji pakietu .NET Core SDK w pliku global.json</span><span class="sxs-lookup"><span data-stu-id="3c4c6-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="3c4c6-121">Jeśli rozwiązanie opiera się na pliku [global.json](/dotnet/core/tools/global-json) do kierowania określonej wersji `version` .NET Core SDK, zaktualizuj jego właściwość, aby użyć wersji 2.0 zainstalowanej na komputerze:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="3c4c6-122">Aktualizuj odwołania do pakietów</span><span class="sxs-lookup"><span data-stu-id="3c4c6-122">Update package references</span></span>

<span data-ttu-id="3c4c6-123">Plik *csproj* w projekcie 1.x wyświetla listę każdego pakietu NuGet używanego przez projekt.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="3c4c6-124">W projekcie ASP.NET Core 2.0 ukierunkowanym na .NET Core 2.0 pojedyncze odwołanie do [metapakiety](xref:fundamentals/metapackage) w pliku *csproj* zastępuje kolekcję pakietów:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="3c4c6-125">Wszystkie funkcje ASP.NET Core 2.0 i Entity Framework Core 2.0 są zawarte w metapakiecie.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="3c4c6-126">ASP.NET core 2.0 projektów kierowania .NET Framework powinny nadal odwoływać się do poszczególnych pakietów NuGet.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="3c4c6-127">Zaktualizuj `Version` `<PackageReference />` atrybut każdego węzła do 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="3c4c6-128">Oto na przykład lista węzłów używanych `<PackageReference />` w typowym projekcie ASP.NET Core 2.0 przeznaczonym dla platformy .NET Framework:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="3c4c6-129">Aktualizowanie narzędzi interfejsu wiersza polecenia core platformy .NET</span><span class="sxs-lookup"><span data-stu-id="3c4c6-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="3c4c6-130">W pliku *csproj* zaktualizuj `Version` `<DotNetCliToolReference />` atrybut każdego węzła do wersji 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="3c4c6-131">Oto na przykład lista narzędzi interfejsu wiersza polecenia używanych w typowym projekcie ASP.NET Core 2.0 przeznaczonym dla platformy .NET Core 2.0:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="3c4c6-132">Zmień nazwę właściwości rezerwowej obiektu docelowego pakietu</span><span class="sxs-lookup"><span data-stu-id="3c4c6-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="3c4c6-133">W pliku *csproj* projektu 1.x `PackageTargetFallback` użyto węzła i zmiennej:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="3c4c6-134">Zmień nazwę węzła i `AssetTargetFallback`zmiennej na:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="3c4c6-135">Aktualizuj metodę główną w Program.cs</span><span class="sxs-lookup"><span data-stu-id="3c4c6-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="3c4c6-136">W projektach 1.x `Main` metoda *Program.cs* wyglądała następująco:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="3c4c6-137">W projektach 2.0 `Main` uproszczono metodę *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="3c4c6-138">Przyjęcie tego nowego wzorca 2.0 jest wysoce zalecane i jest wymagane dla funkcji produktu, takich jak [migracje podstawowe entity framework (EF)](xref:data/ef-mvc/migrations) do pracy.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="3c4c6-139">Na przykład `Update-Database` uruchomienie z okna Konsoli `dotnet ef database update` Menedżera pakietów lub z wiersza polecenia (w projektach przekonwertowanych na ASP.NET Core 2.0) generuje następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="3c4c6-140">Dodawanie dostawców konfiguracji</span><span class="sxs-lookup"><span data-stu-id="3c4c6-140">Add configuration providers</span></span>

<span data-ttu-id="3c4c6-141">W projektach 1.x dodawanie dostawców konfiguracji do `Startup` aplikacji zostało wykonane za pomocą konstruktora.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="3c4c6-142">Kroki polegały na utworzeniu `ConfigurationBuilder`wystąpienia, załadowaniu odpowiednich dostawców (zmienne środowiskowe, ustawienia `IConfigurationRoot`aplikacji itp.) i zainicjowaniu członka .</span><span class="sxs-lookup"><span data-stu-id="3c4c6-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="3c4c6-143">W poprzednim przykładzie `Configuration` ładuje element członkowski z ustawieniami konfiguracji z *appsettings.json,* jak również wszelkie *appsettings.\< EnvironmentName\>.json* plik `IHostingEnvironment.EnvironmentName` pasujący do właściwości.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="3c4c6-144">Lokalizacja tych plików jest na tej samej ścieżce co *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="3c4c6-145">W projektach 2.0 kod konfiguracji standardowej związany z projektami 1.x działa za kulisami.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="3c4c6-146">Na przykład zmienne środowiskowe i ustawienia aplikacji są ładowane podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="3c4c6-147">Równoważny kod *Startup.cs* jest zredukowany do `IConfiguration` inicjowania z wstrzykniętym wystąpieniem:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="3c4c6-148">Aby usunąć domyślnych `WebHostBuilder.CreateDefaultBuilder`dostawców dodanych `Clear` przez `IConfigurationBuilder.Sources` , wywołać metodę na właściwości wewnątrz . `ConfigureAppConfiguration`</span><span class="sxs-lookup"><span data-stu-id="3c4c6-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="3c4c6-149">Aby dodać dostawców z `ConfigureAppConfiguration` powrotem, należy wykorzystać metodę w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="3c4c6-150">Konfigurację używaną `CreateDefaultBuilder` przez metodę w poprzednim fragmentie kodu można zobaczyć [tutaj](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="3c4c6-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="3c4c6-151">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="3c4c6-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="3c4c6-152">Przenieś kod inicjowania bazy danych</span><span class="sxs-lookup"><span data-stu-id="3c4c6-152">Move database initialization code</span></span>

<span data-ttu-id="3c4c6-153">W projektach 1.x przy użyciu EF Core `dotnet ef migrations add` 1.x, polecenie, takie jak następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="3c4c6-154">Tworzenie wystąpienia `Startup` wystąpienia</span><span class="sxs-lookup"><span data-stu-id="3c4c6-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="3c4c6-155">Wywołuje `ConfigureServices` metodę rejestrowania wszystkich usług z iniekcji zależności (w tym `DbContext` typów)</span><span class="sxs-lookup"><span data-stu-id="3c4c6-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="3c4c6-156">Wykonuje wymagane zadania</span><span class="sxs-lookup"><span data-stu-id="3c4c6-156">Performs its requisite tasks</span></span>

<span data-ttu-id="3c4c6-157">W projektach 2.0 przy użyciu EF `Program.BuildWebHost` Core 2.0, jest wywoływana w celu uzyskania usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="3c4c6-158">W przeciwieństwie do 1.x, ma to `Startup.Configure`dodatkowy efekt uboczny wywoływania .</span><span class="sxs-lookup"><span data-stu-id="3c4c6-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="3c4c6-159">Jeśli aplikacja 1.x wywoływał kod inicjowania bazy danych w swojej `Configure` metodzie, mogą wystąpić nieoczekiwane problemy.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="3c4c6-160">Na przykład jeśli baza danych jeszcze nie istnieje, kod rozmieszczenia jest uruchamiany przed wykonaniem polecenia Ef Core Migrations.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="3c4c6-161">Ten problem `dotnet ef migrations list` powoduje, że polecenie zakończy się niepowodzeniem, jeśli baza danych jeszcze nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="3c4c6-162">Rozważmy następujący kod inicjowania materiału `Configure` siewnego 1.x w metodzie *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="3c4c6-163">W projektach 2.0 `SeedData.Initialize` przenieś `Main` wezwanie do metody *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="3c4c6-164">Od 2.0, to zła praktyka, aby `BuildWebHost` zrobić wszystko, z wyjątkiem kompilacji i konfigurowania hosta sieci web.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="3c4c6-165">Wszystko, co dotyczy uruchamiania aplikacji, powinno `BuildWebHost` &mdash; być obsługiwane `Main` poza zazwyczaj metodą *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a><span data-ttu-id="3c4c6-166">Przejrzyj ustawienie kompilacji widoku Razor</span><span class="sxs-lookup"><span data-stu-id="3c4c6-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="3c4c6-167">Krótszy czas uruchamiania aplikacji i mniejsze opublikowane pakiety mają dla Ciebie ogromne znaczenie.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="3c4c6-168">Z tych powodów [kompilacja widoku Razor](xref:mvc/views/view-compilation) jest domyślnie włączona w ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="3c4c6-169">Ustawienie `MvcRazorCompileOnPublish` właściwości true nie jest już wymagane.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="3c4c6-170">Jeśli nie wyłączasz kompilacji widoku, właściwość może zostać usunięta z pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="3c4c6-171">Podczas kierowania .NET Framework nadal należy jawnie odwoływać się do pakietu [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet w pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="3c4c6-172">Polegaj na funkcjach "light-up" usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="3c4c6-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="3c4c6-173">Bezproblemowa konfiguracja oprzyrządowania wydajności aplikacji jest ważna.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="3c4c6-174">Teraz można polegać na nowych funkcji "light-up" [usługi Application Insights](/azure/application-insights/app-insights-overview) dostępnych w narzędziach programu Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="3c4c6-175">ASP.NET projektów core 1.1 utworzonych w programie Visual Studio 2017 domyślnie dodano usługa Application Insights.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="3c4c6-176">Jeśli nie używasz sdk usługi Application Insights bezpośrednio, poza *Program.cs* i *Startup.cs,* wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="3c4c6-177">Jeśli obiekt .NET Core jest `<PackageReference />` skierowany do grupy .NET Core, usuń następujący węzeł z pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="3c4c6-178">Jeśli kierowanie .NET Core, usuń wywołanie `UseApplicationInsights` metody rozszerzenia z *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="3c4c6-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="3c4c6-179">Usuń wywołanie interfejsu API po stronie klienta usługi Application Insights z *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="3c4c6-180">Składa się z następujących dwóch wierszy kodu:</span><span class="sxs-lookup"><span data-stu-id="3c4c6-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="3c4c6-181">Jeśli używasz sdk usługi Application Insights bezpośrednio, kontynuuj to.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="3c4c6-182">[Metapakiet](xref:fundamentals/metapackage) 2.0 zawiera najnowszą wersję usługi Application Insights, więc błąd zmiany wersji pakietu pojawia się, jeśli odwołujesz się do starszej wersji.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a><span data-ttu-id="3c4c6-183">Przyjęcie ulepszeń uwierzytelniania/tożsamości</span><span class="sxs-lookup"><span data-stu-id="3c4c6-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="3c4c6-184">ASP.NET Core 2.0 ma nowy model uwierzytelniania i szereg istotnych zmian w ASP.NET tożsamości podstawowej.</span><span class="sxs-lookup"><span data-stu-id="3c4c6-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="3c4c6-185">Jeśli projekt został utworzony z włączoną obsługą indywidualnych kont użytkowników lub jeśli zostało ręcznie dodane uwierzytelnianie lub tożsamość, zobacz [Migrowanie uwierzytelniania i tożsamości, aby ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="3c4c6-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c4c6-186">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3c4c6-186">Additional resources</span></span>

* [<span data-ttu-id="3c4c6-187">Przełomowe zmiany w ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="3c4c6-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
