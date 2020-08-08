---
title: Razorkompilacja pliku w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kompilacja Razor plików występuje w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: fc7924f8f8b321ae017b7acd729fe11c4e0e3c7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021084"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razorkompilacja pliku w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

RazorPliki z rozszerzeniem *. cshtml* są kompilowane w czasie kompilacji i publikowania przy użyciu [ Razor zestawu SDK](xref:razor-pages/sdk). Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie projektu.

## <a name="no-locrazor-compilation"></a>Razorkompilowa

Kompilacja plików w czasie kompilacji i czas publikowania Razor jest domyślnie włączona przez Razor zestaw SDK. Po włączeniu Kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając Razor aktualizowanie plików, jeśli są edytowane.

## <a name="enable-runtime-compilation-at-project-creation"></a>Włącz kompilację środowiska uruchomieniowego podczas tworzenia projektu

RazorStrony i szablony projektów MVC zawierają opcję włączenia kompilacji środowiska uruchomieniowego podczas tworzenia projektu. Ta opcja jest obsługiwana w ASP.NET Core 3,1 i nowszych.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** :

1. Wybierz szablon projektu aplikacja sieci **Web** lub **aplikacja sieci Web (Model-View-Controller)** .
1. Zaznacz pole wyboru **Włącz Razor kompilację środowiska uruchomieniowego** .

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Użyj `-rrc` `--razor-runtime-compilation` opcji szablonu lub. Na przykład następujące polecenie tworzy nowy Razor Projekt strony z włączoną kompilacją środowiska uruchomieniowego:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Włącz kompilację środowiska uruchomieniowego w istniejącym projekcie

Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk w istniejącym projekcie:

1. Zainstaluj [pakiet Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pakiet NuGet RuntimeCompilation.
1. Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu dołączenia wywołania do <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Przykład:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Warunkowe włączenie kompilacji środowiska uruchomieniowego w istniejącym projekcie

Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko na potrzeby lokalnego tworzenia. Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:

* Używa skompilowanych widoków.
* Nie włącza obserwatorów plików w środowisku produkcyjnym.

Aby włączyć kompilację środowiska uruchomieniowego tylko w środowisku deweloperskim:

1. Zainstaluj [pakiet Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pakiet NuGet RuntimeCompilation.
1. Zmodyfikuj sekcję uruchamianie profilu `environmentVariables` w *launchSettings.jsna*:
    * Sprawdź `ASPNETCORE_ENVIRONMENT` , czy jest ustawiona na `"Development"` .
    * Ustaw `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` wartość `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` .

W poniższym przykładzie Kompilacja środowiska uruchomieniowego jest włączona w środowisku programistycznym dla `IIS Express` profilów i `RazorPagesApp` uruchamiania:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

W klasie projektu nie są wymagane żadne zmiany w kodzie `Startup` . W czasie wykonywania ASP.NET Core wyszukuje [atrybut HostingStartup na poziomie zestawu](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) w `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . Ten `HostingStartup` atrybut określa kod uruchomienia aplikacji do wykonania. Ten kod uruchamiania umożliwia kompilację środowiska uruchomieniowego.

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>Włącz kompilację środowiska uruchomieniowego dla Razor biblioteki klas

Rozważmy scenariusz, w którym Razor Projekt strony odwołuje się do [ Razor biblioteki klas (RCL)](xref:razor-pages/ui-class) o nazwie *MyClassLib*. RCL _Layout zawiera plik *. cshtml* , który jest używany przez wszystkie projekty MVC i Razor Pages zespołu. Chcesz włączyć kompilację środowiska uruchomieniowego dla pliku *_Layout. cshtml* w tym RCL. Wprowadź następujące zmiany w Razor projekcie stron:

1. Włącz kompilację środowiska uruchomieniowego z instrukcjami w [warunkowo Włącz kompilację środowiska uruchomieniowego w istniejącym projekcie](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Skonfiguruj opcje kompilacji środowiska uruchomieniowego w programie `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    W poprzednim kodzie skonstruowana jest ścieżka bezwzględna do *MyClassLib* RCL. [Interfejs API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) jest używany do lokalizowania katalogów i plików w tej ścieżce bezwzględnej. Na koniec `PhysicalFileProvider` wystąpienie jest dodawane do kolekcji dostawców plików, co umożliwia dostęp do plików *. cshtml* RCL.

## <a name="additional-resources"></a>Zasoby dodatkowe

* Właściwości [ Razor CompileOnBuild i Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

RazorPliki z rozszerzeniem *. cshtml* są kompilowane w czasie kompilacji i publikowania przy użyciu [ Razor zestawu SDK](xref:razor-pages/sdk). Kompilacja środowiska uruchomieniowego może być opcjonalnie włączona przez skonfigurowanie aplikacji.

## <a name="no-locrazor-compilation"></a>Razorkompilowa

Kompilacja plików w czasie kompilacji i czas publikowania Razor jest domyślnie włączona przez Razor zestaw SDK. Po włączeniu Kompilacja środowiska uruchomieniowego uzupełnia kompilację w czasie kompilacji, umożliwiając Razor aktualizowanie plików, jeśli są edytowane.

## <a name="runtime-compilation"></a>Kompilacja środowiska uruchomieniowego

Aby włączyć kompilację środowiska uruchomieniowego dla wszystkich środowisk i trybów konfiguracji:

1. Zainstaluj [pakiet Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pakiet NuGet RuntimeCompilation.

1. Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu dołączenia wywołania do <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Przykład:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Warunkowe włączenie kompilacji środowiska uruchomieniowego

Kompilacja środowiska uruchomieniowego może być włączona w taki sposób, że jest dostępna tylko na potrzeby lokalnego tworzenia. Warunkowe włączenie w ten sposób zapewnia, że opublikowane dane wyjściowe:

* Używa skompilowanych widoków.
* Jest mniejszy niż rozmiar.
* Nie włącza obserwatorów plików w środowisku produkcyjnym.

Aby włączyć kompilację środowiska uruchomieniowego na podstawie trybu środowiska i konfiguracji:

1. Warunkowo odwołuje się do [Microsoft. AspNetCore. MVC. Razor .. Pakiet RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) na podstawie aktywnej `Configuration` wartości:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu dołączenia wywołania do `AddRazorRuntimeCompilation` . Warunkowo `AddRazorRuntimeCompilation` uruchamiaj w taki sposób, że działa tylko w trybie debugowania, gdy `ASPNETCORE_ENVIRONMENT` zmienna jest ustawiona na `Development` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* Właściwości [ Razor CompileOnBuild i Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Zapoznaj się z przykładem [Kompilacja środowiska uruchomieniowego w witrynie GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) , aby uzyskać przykład pokazujący, jak działa Kompilacja środowiska uruchomieniowego między projektami.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

RazorPlik jest kompilowany w czasie wykonywania, gdy Razor zostanie wywołana skojarzona Strona lub widok MVC. Razorpliki są kompilowane w czasie kompilacji i publikowania przy użyciu [ Razor zestawu SDK](xref:razor-pages/sdk).

## <a name="no-locrazor-compilation"></a>Razorkompilowa

Kompilacja i czas publikowania Razor plików są domyślnie włączone przez Razor zestaw SDK. Edytowanie Razor plików po ich aktualizacji jest obsługiwane w czasie kompilacji. Domyślnie tylko skompilowane pliki *Views.dll* i No *. cshtml* lub zestawy odwołań wymagane do kompilowania Razor plików są wdrażane przy użyciu aplikacji.

> [!IMPORTANT]
> Narzędzie prekompilacji jest przestarzałe i zostanie usunięte w ASP.NET Core 3,0. Zalecamy Migrowanie do [ Razor zestawu SDK](xref:razor-pages/sdk).
>
> RazorZestaw SDK działa tylko wtedy, gdy w pliku projektu nie są ustawione właściwości specyficzne dla prekompilacji. Na przykład ustawienie właściwości pliku *. csproj* powoduje `MvcRazorCompileOnPublish` `true` wyłączenie Razor zestawu SDK.

## <a name="runtime-compilation"></a>Kompilacja środowiska uruchomieniowego

Kompilacja w czasie kompilacji jest uzupełniana przez kompilację plików w czasie wykonywania Razor . ASP.NET Core MVC ponownie kompiluje Razor pliki, gdy zostanie zmieniona zawartość pliku *. cshtml* .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
