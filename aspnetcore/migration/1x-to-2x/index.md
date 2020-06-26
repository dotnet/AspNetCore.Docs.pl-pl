---
title: Migrowanie z ASP.NET Core 1. x do 2,0
author: scottaddie
description: W tym artykule opisano wymagania wstępne i Najczęstsze kroki migrowania projektu ASP.NET Core 1. x do ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/index
ms.openlocfilehash: 97fe2f36aed4a2ac60a7ffc30ede5e682a838e5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408698"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="0c706-103">Migrowanie z ASP.NET Core 1. x do 2,0</span><span class="sxs-lookup"><span data-stu-id="0c706-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="0c706-104">Przez [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0c706-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0c706-105">W tym artykule przeprowadzimy Cię przez aktualizację istniejącego projektu ASP.NET Core 1. x do ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="0c706-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="0c706-106">Migrowanie aplikacji do ASP.NET Core 2,0 pozwala korzystać z [wielu nowych funkcji i ulepszeń wydajności](xref:aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="0c706-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="0c706-107">Istniejące aplikacje ASP.NET Core 1. x są oparte na szablonach projektu specyficznych dla wersji.</span><span class="sxs-lookup"><span data-stu-id="0c706-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="0c706-108">W miarę rozwoju struktury ASP.NET Core, więc szablony projektu i początkowy kod zawarty w nich.</span><span class="sxs-lookup"><span data-stu-id="0c706-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="0c706-109">Oprócz aktualizowania platformy ASP.NET Core, należy zaktualizować kod aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c706-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="0c706-110">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0c706-110">Prerequisites</span></span>

<span data-ttu-id="0c706-111">Zobacz [wprowadzenie do ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="0c706-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="0c706-112">Aktualizuj moniker platformy docelowej (TFM)</span><span class="sxs-lookup"><span data-stu-id="0c706-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="0c706-113">Projekty ukierunkowane na platformę .NET Core powinny korzystać z [TFM](/dotnet/standard/frameworks) wersji nowszej niż lub równej platformie .net Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="0c706-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="0c706-114">Wyszukaj `<TargetFramework>` węzeł w pliku *. csproj* i Zastąp tekst wewnętrzny tekstem `netcoreapp2.0` :</span><span class="sxs-lookup"><span data-stu-id="0c706-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="0c706-115">Projekty ukierunkowane na .NET Framework powinny używać TFM wersji większej lub równej .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="0c706-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="0c706-116">Wyszukaj `<TargetFramework>` węzeł w pliku *. csproj* i Zastąp tekst wewnętrzny tekstem `net461` :</span><span class="sxs-lookup"><span data-stu-id="0c706-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="0c706-117">Program .NET Core 2,0 oferuje znacznie większy obszar powierzchni niż .NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="0c706-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="0c706-118">Jeśli chcesz, aby .NET Framework tylko z powodu brakujących interfejsów API w programie .NET Core 1. x, dla których będzie możliwe działanie programu .NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="0c706-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="0c706-119">Jeśli plik projektu zawiera `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>` , zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="0c706-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="0c706-120">Aktualizacja zestaw .NET Core SDK wersji w global.jsna</span><span class="sxs-lookup"><span data-stu-id="0c706-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="0c706-121">Jeśli rozwiązanie jest oparte na [global.jsw](/dotnet/core/tools/global-json) pliku przeznaczonym dla konkretnej wersji zestaw .NET Core SDK, zaktualizuj swoją `version` Właściwość, aby użyć wersji 2,0 zainstalowanej na maszynie:</span><span class="sxs-lookup"><span data-stu-id="0c706-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="0c706-122">Aktualizuj odwołania do pakietów</span><span class="sxs-lookup"><span data-stu-id="0c706-122">Update package references</span></span>

<span data-ttu-id="0c706-123">Plik *. csproj* w projekcie 1. x wyświetla każdy pakiet NuGet używany przez ten projekt.</span><span class="sxs-lookup"><span data-stu-id="0c706-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="0c706-124">W projekcie ASP.NET Core 2,0 docelowym .NET Core 2,0, pojedyncze odwołanie do [pakietu](xref:fundamentals/metapackage) w pliku *csproj* zastępuje kolekcję pakietów:</span><span class="sxs-lookup"><span data-stu-id="0c706-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="0c706-125">Wszystkie funkcje ASP.NET Core 2,0 i Entity Framework Core 2,0 są zawarte w pakiecie.</span><span class="sxs-lookup"><span data-stu-id="0c706-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="0c706-126">Projekty ASP.NET Core 2,0 .NET Framework powinny nadal odwoływać się do poszczególnych pakietów NuGet.</span><span class="sxs-lookup"><span data-stu-id="0c706-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="0c706-127">Zaktualizuj `Version` atrybut każdego `<PackageReference />` węzła do 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="0c706-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="0c706-128">Na przykład poniżej znajduje się lista `<PackageReference />` węzłów używanych w typowym .NET Framework projektu ASP.NET Core 2,0:</span><span class="sxs-lookup"><span data-stu-id="0c706-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="0c706-129">Narzędzia aktualizacji interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0c706-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="0c706-130">W pliku *. csproj* zaktualizuj `Version` atrybut każdego `<DotNetCliToolReference />` węzła do 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="0c706-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="0c706-131">Na przykład poniżej przedstawiono listę narzędzi interfejsu wiersza polecenia używanych w typowym projekcie ASP.NET Core 2,0, dla którego jest przeznaczony program .NET Core 2,0:</span><span class="sxs-lookup"><span data-stu-id="0c706-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="0c706-132">Zmień nazwę właściwości rezerwowej elementu docelowego pakietu</span><span class="sxs-lookup"><span data-stu-id="0c706-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="0c706-133">Plik *. csproj* projektu 1. x użył `PackageTargetFallback` węzła i zmiennej:</span><span class="sxs-lookup"><span data-stu-id="0c706-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="0c706-134">Zmień nazwę węzła i zmiennej na `AssetTargetFallback` :</span><span class="sxs-lookup"><span data-stu-id="0c706-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="0c706-135">Aktualizowanie metody Main w Program.cs</span><span class="sxs-lookup"><span data-stu-id="0c706-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="0c706-136">W projektach 1. x `Main` metoda *program.cs* wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="0c706-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="0c706-137">W przypadku projektów 2,0 `Main` metoda *program.cs* została uproszczona:</span><span class="sxs-lookup"><span data-stu-id="0c706-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="0c706-138">Przyjęcie tego nowego wzorca 2,0 jest zdecydowanie zalecane i jest wymagane do pracy z funkcjami produktu, takimi jak [Entity Framework (EF) Core](xref:data/ef-mvc/migrations) .</span><span class="sxs-lookup"><span data-stu-id="0c706-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="0c706-139">Na przykład uruchomienie `Update-Database` z okna konsoli Menedżera pakietów lub `dotnet ef database update` z wiersza polecenia (w projektach przekonwertowanych na ASP.NET Core 2,0) generuje następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="0c706-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="0c706-140">Dodawanie dostawców konfiguracji</span><span class="sxs-lookup"><span data-stu-id="0c706-140">Add configuration providers</span></span>

<span data-ttu-id="0c706-141">W projektach 1. x Dodawanie dostawców konfiguracji do aplikacji zostało zrealizowane za pośrednictwem `Startup` konstruktora.</span><span class="sxs-lookup"><span data-stu-id="0c706-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="0c706-142">Kroki związane z tworzeniem wystąpienia `ConfigurationBuilder` , ładowanie odpowiednich dostawców (zmienne środowiskowe, ustawienia aplikacji itp.) i inicjowanie elementu członkowskiego `IConfigurationRoot` .</span><span class="sxs-lookup"><span data-stu-id="0c706-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="0c706-143">W powyższym przykładzie załaduje `Configuration` element członkowski z ustawieniami konfiguracji z *appsettings.jsna* , a także z dowolnym *appSettings. \<EnvironmentName\> plik JSON* pasuje do `IHostingEnvironment.EnvironmentName` właściwości.</span><span class="sxs-lookup"><span data-stu-id="0c706-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="0c706-144">Lokalizacja tych plików jest taka sama jak ścieżka *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0c706-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="0c706-145">W projektach 2,0, kod konfiguracji standardowa nieodłącz się od projektów 1. x działa w tle.</span><span class="sxs-lookup"><span data-stu-id="0c706-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="0c706-146">Na przykład zmienne środowiskowe i ustawienia aplikacji są ładowane podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="0c706-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="0c706-147">Równoważny kod *Startup.cs* został zredukowany do `IConfiguration` inicjacji z wstrzykiwanym wystąpieniem:</span><span class="sxs-lookup"><span data-stu-id="0c706-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="0c706-148">Aby usunąć domyślnych dostawców dodanych przez `WebHostBuilder.CreateDefaultBuilder` , wywołaj `Clear` metodę we `IConfigurationBuilder.Sources` właściwości w `ConfigureAppConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="0c706-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="0c706-149">Aby dodać dostawców z powrotem, użyj `ConfigureAppConfiguration` metody w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0c706-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="0c706-150">`CreateDefaultBuilder`W [tym miejscu](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152)można zobaczyć konfigurację używaną przez metodę w poprzednim fragmencie kodu.</span><span class="sxs-lookup"><span data-stu-id="0c706-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="0c706-151">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="0c706-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="0c706-152">Przenieś kod inicjalizacji bazy danych</span><span class="sxs-lookup"><span data-stu-id="0c706-152">Move database initialization code</span></span>

<span data-ttu-id="0c706-153">W projektach 1. x używających EF Core 1. x, polecenie takie jak `dotnet ef migrations add` wykonuje następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="0c706-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="0c706-154">Tworzy `Startup` wystąpienie wystąpienia</span><span class="sxs-lookup"><span data-stu-id="0c706-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="0c706-155">Wywołuje `ConfigureServices` metodę, aby zarejestrować wszystkie usługi przy użyciu iniekcji zależności (w tym `DbContext` typów)</span><span class="sxs-lookup"><span data-stu-id="0c706-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="0c706-156">Wykonuje wymagane zadania</span><span class="sxs-lookup"><span data-stu-id="0c706-156">Performs its requisite tasks</span></span>

<span data-ttu-id="0c706-157">W przypadku projektów 2,0 przy użyciu EF Core 2,0 `Program.BuildWebHost` jest wywoływana w celu uzyskania usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c706-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="0c706-158">W przeciwieństwie do 1. x, ma to dodatkowy efekt uboczny wywoływania `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0c706-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="0c706-159">Jeśli aplikacja 1. x wywołała kod inicjalizacji bazy danych w swojej `Configure` metodzie, mogą wystąpić nieoczekiwane problemy.</span><span class="sxs-lookup"><span data-stu-id="0c706-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="0c706-160">Na przykład jeśli baza danych jeszcze nie istnieje, kod inicjujący jest uruchamiany przed wykonaniem polecenia EF Core migracji.</span><span class="sxs-lookup"><span data-stu-id="0c706-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="0c706-161">Ten problem powoduje, że `dotnet ef migrations list` polecenie kończy się niepowodzeniem, jeśli baza danych jeszcze nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="0c706-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="0c706-162">Rozważmy następujący kod inicjujący inicjatora 1. x w `Configure` metodzie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0c706-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="0c706-163">W projektach 2,0 Przenieś `SeedData.Initialize` wywołanie do `Main` metody *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0c706-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="0c706-164">Począwszy od 2,0, jest to niewłaściwe rozwiązanie w przypadku `BuildWebHost` kompilacji i konfigurowania hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="0c706-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="0c706-165">Wszystkie informacje o działaniu aplikacji powinny być obsługiwane poza `BuildWebHost` &mdash; zwykle w `Main` metodzie *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="0c706-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a><span data-ttu-id="0c706-166">Przegląd Razor Ustawienia kompilacji widoku</span><span class="sxs-lookup"><span data-stu-id="0c706-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="0c706-167">Skrócenie czasu uruchamiania aplikacji i mniejszych opublikowanych pakietów mają na celu najwyższą ważność.</span><span class="sxs-lookup"><span data-stu-id="0c706-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="0c706-168">Z tego względu [ Razor kompilacja widoku](xref:mvc/views/view-compilation) jest domyślnie włączona w ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="0c706-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="0c706-169">Ustawienie `MvcRazorCompileOnPublish` właściwości na wartość true nie jest już wymagane.</span><span class="sxs-lookup"><span data-stu-id="0c706-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="0c706-170">Jeśli nie wyłączysz kompilacji widoku, właściwość może zostać usunięta z pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0c706-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="0c706-171">Podczas określania wartości docelowej .NET Framework nadal trzeba jawnie odwoływać się do [Microsoft. AspNetCore. MVC. Razor . ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) pakiet NuGet w pliku *csproj* :</span><span class="sxs-lookup"><span data-stu-id="0c706-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="0c706-172">Korzystaj z funkcji "lekkich" Application Insights</span><span class="sxs-lookup"><span data-stu-id="0c706-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="0c706-173">Istotna konfiguracja Instrumentacji wydajności aplikacji jest bardzo ważna.</span><span class="sxs-lookup"><span data-stu-id="0c706-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="0c706-174">Teraz można polegać na nowych funkcjach [Application Insights](/azure/application-insights/app-insights-overview) "świateł-up" dostępnych w narzędziach programu Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="0c706-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="0c706-175">Projekty ASP.NET Core 1,1 utworzone w programie Visual Studio 2017 zostały dodane domyślnie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="0c706-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="0c706-176">Jeśli nie używasz bezpośrednio zestawu SDK Application Insights, poza *program.cs* i *Startup.cs*, wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="0c706-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="0c706-177">Jeśli celem jest .NET Core, usuń następujący `<PackageReference />` węzeł z pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="0c706-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="0c706-178">Jeśli celem jest .NET Core, Usuń `UseApplicationInsights` wywołanie metody rozszerzenia z *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0c706-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="0c706-179">Usuń Application Insights wywołanie interfejsu API po stronie klienta z *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0c706-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="0c706-180">Obejmuje dwa następujące wiersze kodu:</span><span class="sxs-lookup"><span data-stu-id="0c706-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="0c706-181">Jeśli używasz bezpośrednio zestawu SDK Application Insights, Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="0c706-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="0c706-182">[Pakiet](xref:fundamentals/metapackage) "2,0" zawiera najnowszą wersję Application Insights, więc w przypadku odwoływania się do starszej wersji pojawia się błąd obniżenia poziomu pakietów.</span><span class="sxs-lookup"><span data-stu-id="0c706-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a><span data-ttu-id="0c706-183">Przyjmowanie uwierzytelniania/ Identity ulepszeń</span><span class="sxs-lookup"><span data-stu-id="0c706-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="0c706-184">ASP.NET Core 2,0 ma nowy model uwierzytelniania i wiele znaczących zmian ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="0c706-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="0c706-185">Jeśli projekt został utworzony z włączonymi indywidualnymi kontami użytkowników lub jeśli masz ręcznie dodane uwierzytelnianie lub Identity , zobacz [Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="0c706-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c706-186">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="0c706-186">Additional resources</span></span>

* [<span data-ttu-id="0c706-187">Istotne zmiany w ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="0c706-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
