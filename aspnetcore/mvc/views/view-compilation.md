---
title: Kompilacja plików brzytwy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak odbywa się kompilacja plików Razor w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440938"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilacja plików brzytwy w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Pliki razor z rozszerzeniem *.cshtml* są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk). Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie projektu.

## <a name="razor-compilation"></a>Kompilacja maszynki do golenia

Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK. Po włączeniu kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając aktualizowanie plików Razor, jeśli są edytowane.

## <a name="enable-runtime-compilation-at-project-creation"></a>Włączanie kompilacji środowiska uruchomieniowego podczas tworzenia projektu

Strony Razor i szablony projektów MVC zawierają opcję włączania kompilacji środowiska uruchomieniowego podczas tworzenia projektu. Ta opcja jest obsługiwana w ASP.NET Core 3.1 i nowszych.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W oknie **dialogowym Tworzenie nowej aplikacji sieci Web ASP.NET Core:**

1. Wybierz szablon projektu **aplikacji sieci Web** lub aplikacji sieci Web **(Model-View-Controller).**
1. Zaznacz pole wyboru **Włącz kompilację środowiska uruchomieniowego Razor.**

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Użyj `-rrc` opcji `--razor-runtime-compilation` lub szablonu. Na przykład następujące polecenie tworzy nowy projekt Razor Pages z włączoną kompilacją środowiska wykonawczego:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Włączanie kompilacji środowiska uruchomieniowego w istniejącym projekcie

Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk w istniejącym projekcie:

1. Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.
1. Zaktualizuj `Startup.ConfigureServices` metodę projektu, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby uwzględnić wywołanie . Przykład:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Warunkowe włączanie kompilacji środowiska uruchomieniowego w istniejącym projekcie

Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko dla rozwoju lokalnego. Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:

* Używa skompilowanych widoków.
* Nie włącza obserwatorów plików w produkcji.

Aby włączyć kompilację środowiska uruchomieniowego tylko w środowisku deweloperskim:

1. Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.
1. Zmodyfikuj sekcję profilu `environmentVariables` uruchamiania w *launchSettings.json*:
    * Sprawdź `ASPNETCORE_ENVIRONMENT` jest `"Development"`ustawiona na .
    * Ustaw `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`na .

W poniższym przykładzie kompilacja środowiska uruchomieniowego jest `IIS Express` `RazorPagesApp` włączona w środowisku programistycznym dla profilów i uruchamiania:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Żadne zmiany kodu nie są `Startup` potrzebne w klasie projektu. W czasie wykonywania ASP.NET Core wyszukuje [atrybut HostingStartup](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`na poziomie zestawu w . Atrybut `HostingStartup` określa kod startowy aplikacji do wykonania. Ten kod startowy umożliwia kompilację środowiska uruchomieniowego.

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a>Włączanie kompilacji środowiska uruchomieniowego dla biblioteki klas Razor

Rozważmy scenariusz, w którym projekt Razor Pages odwołuje się do [biblioteki klas Razor (RCL)](xref:razor-pages/ui-class) o nazwie *MyClassLib*. RCL zawiera *plik _Layout.cshtml,* że wszystkie projekty MVC zespołu i Razor Pages zużywają. Chcesz włączyć kompilację środowiska uruchomieniowego dla pliku *_Layout.cshtml* w tej śr. W projekcie Strony maszynki do golenia wprowadzono następujące zmiany:

1. Włącz kompilację środowiska uruchomieniowego z instrukcjami w [warunkowo włączyć kompilację środowiska uruchomieniowego w istniejącym projekcie](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Konfigurowanie opcji kompilacji środowiska `Startup.ConfigureServices`uruchomieniowego w:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    W poprzednim kodzie jest skonstruowana ścieżka bezwzględna do listy RCL *MyClassLib.* [Interfejs API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) służy do lokalizowania katalogów i plików w tej ścieżce bezwzględnej. Na koniec `PhysicalFileProvider` wystąpienie jest dodawane do kolekcji dostawców plików, co umożliwia dostęp do plików *.cshtml* rcl.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Właściwości RazorCompileOnBuild i RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Pliki razor z rozszerzeniem *.cshtml* są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk). Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie aplikacji.

## <a name="razor-compilation"></a>Kompilacja maszynki do golenia

Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK. Po włączeniu kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając aktualizowanie plików Razor, jeśli są edytowane.

## <a name="runtime-compilation"></a>Kompilacja środowiska uruchomieniowego

Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk i trybów konfiguracji:

1. Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.

1. Zaktualizuj `Startup.ConfigureServices` metodę projektu, <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>aby uwzględnić wywołanie . Przykład:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Warunkowe włączanie kompilacji środowiska uruchomieniowego

Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko dla rozwoju lokalnego. Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:

* Używa skompilowanych widoków.
* Jest mniejszy.
* Nie włącza obserwatorów plików w produkcji.

Aby włączyć kompilację środowiska uruchomieniowego opartą na środowisku i trybie konfiguracji:

1. Warunkowo odwołaj się do pakietu [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) na podstawie wartości aktywnej: `Configuration`

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Zaktualizuj `Startup.ConfigureServices` metodę projektu, `AddRazorRuntimeCompilation`aby uwzględnić wywołanie . Warunkowo `AddRazorRuntimeCompilation` wykonać tak, że działa tylko `ASPNETCORE_ENVIRONMENT` w trybie `Development`debugowania, gdy zmienna jest ustawiona na:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Właściwości RazorCompileOnBuild i RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Zobacz [przykład kompilacji środowiska wykonawczego w usłudze GitHub,](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) aby uzyskać przykład, który pokazuje tworzenie pracy kompilacji środowiska uruchomieniowego między projektami.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Plik Razor jest kompilowany w czasie wykonywania, gdy wywoływana jest skojarzona strona Razor lub widok MVC. Pliki razor są kompilowane zarówno w czasie kompilacji, jak i publikowania przy użyciu [razor SDK](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Kompilacja maszynki do golenia

Kompilacja plików Razor w czasie kompilacji i czasu publikacji jest domyślnie włączona przez zestaw Razor SDK. Edytowanie plików Razor po ich zaktualizowaniu jest obsługiwane w czasie kompilacji. Domyślnie tylko skompilowana *plik Views.dll* i nie *.cshtml* plików lub odwołań zestawów wymaganych do kompilowania plików Razor są wdrażane z aplikacją.

> [!IMPORTANT]
> Narzędzie do wstępnej kompilacji zostało przestarzałe i zostanie usunięte w ASP.NET Core 3.0. Zalecamy migrację do [Razor Sdk](xref:razor-pages/sdk).
>
> Zestaw Razor SDK jest skuteczny tylko wtedy, gdy w pliku projektu nie są ustawione żadne właściwości specyficzne dla prekompilacji. Na przykład ustawienie `MvcRazorCompileOnPublish` właściwości pliku *csproj* `true` w celu wyłączenia SDK Razor.

## <a name="runtime-compilation"></a>Kompilacja środowiska uruchomieniowego

Kompilacja w czasie kompilacji jest uzupełniona kompilacją środowiska wykonawczego plików Razor. ASP.NET Core MVC ponownie skompiluje pliki Razor po zmianie zawartości pliku *cshtml.*

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
