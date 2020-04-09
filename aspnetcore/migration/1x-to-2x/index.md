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
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Migracja z ASP.NET Core 1.x do 2.0

Przez [Scott Addie](https://github.com/scottaddie)

W tym artykule prowadzimy cię przez aktualizację istniejącego projektu core 1.x ASP.NET, aby ASP.NET Core 2.0. Migracja aplikacji do ASP.NET Core 2.0 umożliwia korzystanie z [wielu nowych funkcji i ulepszeń wydajności.](xref:aspnetcore-2.0)

Istniejące aplikacje ASP.NET Core 1.x są oparte na szablonach projektów specyficznych dla wersji. W miarę rozwoju ASP.NET core framework, podobnie jak szablony projektów i kod startowy zawarty w nich. Oprócz aktualizowania ASP.NET framework Core, należy zaktualizować kod dla aplikacji.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Wymagania wstępne

Zobacz [Wprowadzenie do ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Aktualizacja moniker struktury docelowej (TFM)

Projekty przeznaczone dla platformy .NET Core powinny używać [TFM](/dotnet/standard/frameworks) wersji większej lub równej .NET Core 2.0. Wyszukaj `<TargetFramework>` węzeł w pliku *csproj* i zastąp jego wewnętrzny `netcoreapp2.0`tekst:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Projekty przeznaczone dla platformy .NET Framework należy użyć TFM wersji większej lub równej .NET Framework 4.6.1. Wyszukaj `<TargetFramework>` węzeł w pliku *csproj* i zastąp jego wewnętrzny `net461`tekst:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> Program .NET Core 2.0 oferuje znacznie większą powierzchnię niż .NET Core 1.x. Jeśli kierujesz reklamy do platformy .NET Framework wyłącznie z powodu brakujących interfejsów API w systemie .NET Core 1.x, kierowanie na program .NET Core 2.0 prawdopodobnie zadziała.

Jeśli plik projektu `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`zawiera , zobacz [ten problem GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Aktualizowanie wersji pakietu .NET Core SDK w pliku global.json

Jeśli rozwiązanie opiera się na pliku [global.json](/dotnet/core/tools/global-json) do kierowania określonej wersji `version` .NET Core SDK, zaktualizuj jego właściwość, aby użyć wersji 2.0 zainstalowanej na komputerze:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Aktualizuj odwołania do pakietów

Plik *csproj* w projekcie 1.x wyświetla listę każdego pakietu NuGet używanego przez projekt.

W projekcie ASP.NET Core 2.0 ukierunkowanym na .NET Core 2.0 pojedyncze odwołanie do [metapakiety](xref:fundamentals/metapackage) w pliku *csproj* zastępuje kolekcję pakietów:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Wszystkie funkcje ASP.NET Core 2.0 i Entity Framework Core 2.0 są zawarte w metapakiecie.

ASP.NET core 2.0 projektów kierowania .NET Framework powinny nadal odwoływać się do poszczególnych pakietów NuGet. Zaktualizuj `Version` `<PackageReference />` atrybut każdego węzła do 2.0.0.

Oto na przykład lista węzłów używanych `<PackageReference />` w typowym projekcie ASP.NET Core 2.0 przeznaczonym dla platformy .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Aktualizowanie narzędzi interfejsu wiersza polecenia core platformy .NET

W pliku *csproj* zaktualizuj `Version` `<DotNetCliToolReference />` atrybut każdego węzła do wersji 2.0.0.

Oto na przykład lista narzędzi interfejsu wiersza polecenia używanych w typowym projekcie ASP.NET Core 2.0 przeznaczonym dla platformy .NET Core 2.0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Zmień nazwę właściwości rezerwowej obiektu docelowego pakietu

W pliku *csproj* projektu 1.x `PackageTargetFallback` użyto węzła i zmiennej:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Zmień nazwę węzła i `AssetTargetFallback`zmiennej na:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Aktualizuj metodę główną w Program.cs

W projektach 1.x `Main` metoda *Program.cs* wyglądała następująco:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

W projektach 2.0 `Main` uproszczono metodę *Program.cs:*

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Przyjęcie tego nowego wzorca 2.0 jest wysoce zalecane i jest wymagane dla funkcji produktu, takich jak [migracje podstawowe entity framework (EF)](xref:data/ef-mvc/migrations) do pracy. Na przykład `Update-Database` uruchomienie z okna Konsoli `dotnet ef database update` Menedżera pakietów lub z wiersza polecenia (w projektach przekonwertowanych na ASP.NET Core 2.0) generuje następujący błąd:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Dodawanie dostawców konfiguracji

W projektach 1.x dodawanie dostawców konfiguracji do `Startup` aplikacji zostało wykonane za pomocą konstruktora. Kroki polegały na utworzeniu `ConfigurationBuilder`wystąpienia, załadowaniu odpowiednich dostawców (zmienne środowiskowe, ustawienia `IConfigurationRoot`aplikacji itp.) i zainicjowaniu członka .

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

W poprzednim przykładzie `Configuration` ładuje element członkowski z ustawieniami konfiguracji z *appsettings.json,* jak również wszelkie *appsettings.\< EnvironmentName\>.json* plik `IHostingEnvironment.EnvironmentName` pasujący do właściwości. Lokalizacja tych plików jest na tej samej ścieżce co *Startup.cs*.

W projektach 2.0 kod konfiguracji standardowej związany z projektami 1.x działa za kulisami. Na przykład zmienne środowiskowe i ustawienia aplikacji są ładowane podczas uruchamiania. Równoważny kod *Startup.cs* jest zredukowany do `IConfiguration` inicjowania z wstrzykniętym wystąpieniem:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Aby usunąć domyślnych `WebHostBuilder.CreateDefaultBuilder`dostawców dodanych `Clear` przez `IConfigurationBuilder.Sources` , wywołać metodę na właściwości wewnątrz . `ConfigureAppConfiguration` Aby dodać dostawców z `ConfigureAppConfiguration` powrotem, należy wykorzystać metodę w *Program.cs:*

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Konfigurację używaną `CreateDefaultBuilder` przez metodę w poprzednim fragmentie kodu można zobaczyć [tutaj](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Przenieś kod inicjowania bazy danych

W projektach 1.x przy użyciu EF Core `dotnet ef migrations add` 1.x, polecenie, takie jak następujące czynności:

1. Tworzenie wystąpienia `Startup` wystąpienia
1. Wywołuje `ConfigureServices` metodę rejestrowania wszystkich usług z iniekcji zależności (w tym `DbContext` typów)
1. Wykonuje wymagane zadania

W projektach 2.0 przy użyciu EF `Program.BuildWebHost` Core 2.0, jest wywoływana w celu uzyskania usług aplikacji. W przeciwieństwie do 1.x, ma to `Startup.Configure`dodatkowy efekt uboczny wywoływania . Jeśli aplikacja 1.x wywoływał kod inicjowania bazy danych w swojej `Configure` metodzie, mogą wystąpić nieoczekiwane problemy. Na przykład jeśli baza danych jeszcze nie istnieje, kod rozmieszczenia jest uruchamiany przed wykonaniem polecenia Ef Core Migrations. Ten problem `dotnet ef migrations list` powoduje, że polecenie zakończy się niepowodzeniem, jeśli baza danych jeszcze nie istnieje.

Rozważmy następujący kod inicjowania materiału `Configure` siewnego 1.x w metodzie *Startup.cs:*

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

W projektach 2.0 `SeedData.Initialize` przenieś `Main` wezwanie do metody *Program.cs:*

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

Od 2.0, to zła praktyka, aby `BuildWebHost` zrobić wszystko, z wyjątkiem kompilacji i konfigurowania hosta sieci web. Wszystko, co dotyczy uruchamiania aplikacji, powinno `BuildWebHost` &mdash; być obsługiwane `Main` poza zazwyczaj metodą *Program.cs*.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Przejrzyj ustawienie kompilacji widoku Razor

Krótszy czas uruchamiania aplikacji i mniejsze opublikowane pakiety mają dla Ciebie ogromne znaczenie. Z tych powodów [kompilacja widoku Razor](xref:mvc/views/view-compilation) jest domyślnie włączona w ASP.NET Core 2.0.

Ustawienie `MvcRazorCompileOnPublish` właściwości true nie jest już wymagane. Jeśli nie wyłączasz kompilacji widoku, właściwość może zostać usunięta z pliku *csproj.*

Podczas kierowania .NET Framework nadal należy jawnie odwoływać się do pakietu [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet w pliku *csproj:*

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Polegaj na funkcjach "light-up" usługi Application Insights

Bezproblemowa konfiguracja oprzyrządowania wydajności aplikacji jest ważna. Teraz można polegać na nowych funkcji "light-up" [usługi Application Insights](/azure/application-insights/app-insights-overview) dostępnych w narzędziach programu Visual Studio 2017.

ASP.NET projektów core 1.1 utworzonych w programie Visual Studio 2017 domyślnie dodano usługa Application Insights. Jeśli nie używasz sdk usługi Application Insights bezpośrednio, poza *Program.cs* i *Startup.cs,* wykonaj następujące kroki:

1. Jeśli obiekt .NET Core jest `<PackageReference />` skierowany do grupy .NET Core, usuń następujący węzeł z pliku *csproj:*

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Jeśli kierowanie .NET Core, usuń wywołanie `UseApplicationInsights` metody rozszerzenia z *Program.cs:*

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Usuń wywołanie interfejsu API po stronie klienta usługi Application Insights z *_Layout.cshtml*. Składa się z następujących dwóch wierszy kodu:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Jeśli używasz sdk usługi Application Insights bezpośrednio, kontynuuj to. [Metapakiet](xref:fundamentals/metapackage) 2.0 zawiera najnowszą wersję usługi Application Insights, więc błąd zmiany wersji pakietu pojawia się, jeśli odwołujesz się do starszej wersji.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Przyjęcie ulepszeń uwierzytelniania/tożsamości

ASP.NET Core 2.0 ma nowy model uwierzytelniania i szereg istotnych zmian w ASP.NET tożsamości podstawowej. Jeśli projekt został utworzony z włączoną obsługą indywidualnych kont użytkowników lub jeśli zostało ręcznie dodane uwierzytelnianie lub tożsamość, zobacz [Migrowanie uwierzytelniania i tożsamości, aby ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Przełomowe zmiany w ASP.NET Core 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
