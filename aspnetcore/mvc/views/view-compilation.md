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
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilacja plików brzytwy w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

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

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

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
