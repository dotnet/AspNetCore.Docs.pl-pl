---
title: Zestaw Razor SDK ASP.NET Core
author: Rick-Anderson
description: Dowiedz Razor się, jak strony w ASP.NET Core sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: a49f183c4c037a1654e79bdb672b758684137cbe
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776737"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Omówienie

[!INCLUDE[](~/includes/2.1-SDK.md)] Zawiera zestaw SDK `Microsoft.NET.Sdk.Razor` programu MSBuild (Razor SDK). Zestaw SDK Razor:

::: moniker range=">= aspnetcore-3.0"

* Jest wymagana do kompilowania, pakowania i publikowania projektów zawierających pliki [Razor](xref:mvc/views/razor) dla ASP.NET Core projektów opartych na MVC lub [Blazor](xref:blazor/index) .
* Zawiera zestaw wstępnie zdefiniowanych obiektów docelowych, właściwości i elementów, które umożliwiają dostosowanie kompilacji plików Razor (*. cshtml* lub *. Razor*).

Zestaw SDK Razor zawiera `Content` elementy z `Include` atrybutami ustawionymi `**\*.cshtml` na `**\*.razor` wzorce i obsługi symboli wieloznacznych. Zgodne pliki są publikowane.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standaryzacja środowiska tworzenia, pakowania i publikowania projektów zawierających pliki [Razor](xref:mvc/views/razor) dla ASP.NET Core projektów opartych na MVC.
* Zawiera zestaw wstępnie zdefiniowanych obiektów docelowych, właściwości i elementów umożliwiających Dostosowywanie kompilacji plików Razor.

Zestaw SDK Razor zawiera `Content` element z `Include` atrybutem ustawionym na wzorzec `**\*.cshtml` obsługi symboli wieloznacznych. Zgodne pliki są publikowane.

::: moniker-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Korzystanie z zestawu SDK Razor

Większość aplikacji sieci Web nie jest wymagana, aby jawnie odwoływać się do zestawu Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Aby użyć zestawu Razor SDK do kompilowania bibliotek klas zawierających widoki Razor lub Razor Pages, zalecamy rozpoczęcie od szablonu projektu biblioteki klas Razor (RCL). RCL, który jest używany do kompilowania plików Blazor (*. Razor*) w minimalnym stopniu wymaga odwołania do pakietu [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . RCL używany do kompilowania widoków i stron Razor (pliki *. cshtml* ) w minimalnym stopniu wymaga `netcoreapp3.0` obiektu docelowego lub nowszego `FrameworkReference` , a w pliku projektu znajduje się w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aby użyć zestawu Razor SDK do kompilowania bibliotek klas zawierających widoki Razor lub Razor Pages:

* Użyj `Microsoft.NET.Sdk.Razor` zamiast `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Zazwyczaj odwołanie do pakietu `Microsoft.AspNetCore.Mvc` jest wymagane do uzyskania dodatkowych zależności, które są wymagane do kompilowania i kompilowania Razor Pages i widoków Razor. Co najmniej, projekt powinien dodawać odwołania do pakietów do:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` Pakiet zawiera zadania kompilacji Razor i elementy docelowe dla projektu.

  Poprzednie pakiety są zawarte w `Microsoft.AspNetCore.Mvc`. Poniższe znaczniki przedstawiają plik projektu, który używa zestawu Razor SDK do kompilowania plików Razor dla aplikacji Razor Pages ASP.NET Core:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Pakiety `Microsoft.AspNetCore.Razor.Design` i `Microsoft.AspNetCore.Mvc.Razor.Extensions` są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Niemniej jednak dokumentacja `Microsoft.AspNetCore.App` pakietu bez wersji zapewnia pakiet dla aplikacji, która nie zawiera najnowszej wersji programu `Microsoft.AspNetCore.Razor.Design`. Projekty muszą odwoływać się do spójnej `Microsoft.AspNetCore.Razor.Design` wersji `Microsoft.AspNetCore.Mvc`(lub), aby najnowsze poprawki czasu kompilacji dla Razor zostały uwzględnione. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/aspnet/Razor/issues/2553)w serwisie GitHub.

::: moniker-end

### <a name="properties"></a>Właściwości

Następujące właściwości kontrolują zachowanie zestawu SDK Razor w ramach kompilacji projektu:

* `RazorCompileOnBuild`&ndash; Gdy `true`, kompiluje i emituje zestaw Razor w ramach kompilowania projektu. Wartość domyślna `true`to.
* `RazorCompileOnPublish`&ndash; Gdy `true`, kompiluje i emituje zestaw Razor w ramach publikowania projektu. Wartość domyślna `true`to.

Właściwości i elementy w poniższej tabeli służą do konfigurowania danych wejściowych i wyjściowych do zestawu Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Począwszy od ASP.NET Core 3,0, widoki MVC lub Razor Pages nie są obsługiwane domyślnie, `RazorCompileOnBuild` Jeśli właściwości `RazorCompileOnPublish` programu MSBuild w pliku projektu są wyłączone. Aplikacje muszą dodać jawne odwołanie do pakietu [Microsoft. AspNetCore. MVC. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , jeśli aplikacja korzysta z kompilacji środowiska uruchomieniowego w celu przetworzenia plików *. cshtml* .

::: moniker-end

| Items | Opis |
| ----- | ----------- |
| `RazorGenerate` | Elementy elementu (pliki *. cshtml* ), które są danymi wejściowymi do generowania kodu. |
| `RazorComponent` | Elementy elementu (pliki*Razor* ), które są danymi wejściowymi do generowania kodu składnika Razor. |
| `RazorCompile` | Elementy elementów (pliki*CS* ), które są danymi wejściowymi do elementów docelowych kompilacji Razor. Użyj tego `ItemGroup` , aby określić dodatkowe pliki do skompilowania do zestawu Razor. |
| `RazorTargetAssemblyAttribute` | Elementy elementów używane do tworzenia kodu atrybutów dla zestawu Razor. Przykład:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementy elementu dodane jako zasoby osadzone do wygenerowanego zestawu Razor. |

::: moniker range=">= aspnetcore-3.0"

| Właściwość | Opis |
| -------- | ----------- |
| `RazorTargetName` | Nazwa pliku (bez rozszerzenia) zestawu utworzonego przez Razor. |
| `RazorOutputPath` | Katalog wyjściowy Razor. |
| `RazorCompileToolset` | Służy do określenia zestawu narzędzi używanego do tworzenia zestawów Razor. Prawidłowe wartości to `Implicit`, `RazorSDK`, i `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Wartość domyślna to `true`. Gdy `true`, zawiera pliki *Web. config*, *. JSON*i *. cshtml* jako zawartość projektu. Gdy jest przywoływany za pośrednictwem `Microsoft.NET.Sdk.Web`, uwzględniane są również pliki w folderze *wwwroot* i pliki konfiguracji. |
| `EnableDefaultRazorGenerateItems` | Gdy `true`, zawiera pliki *. cshtml* z `Content` elementów w `RazorGenerate` elementach. |
| `GenerateRazorTargetAssemblyInfo` | Gdy `true`program generuje plik *. cs* zawierający atrybuty określone przez `RazorAssemblyAttribute` i zawiera plik w danych wyjściowych kompilacji. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Gdy `true`program dodaje domyślny zestaw atrybutów zestawu do `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Gdy `true`kopiuje `RazorGenerate` pliki elementów (*. cshtml*) do katalogu publikowania. Zazwyczaj pliki Razor nie są wymagane dla opublikowanej aplikacji, jeśli uczestniczą w kompilacji w czasie kompilacji lub w czasie publikacji. Wartość domyślna `false`to. |
| `PreserveCompilationReferences` | Gdy `true`Kopiuj elementy zestawu odwołań do katalogu publikowania. Zazwyczaj zestawy referencyjne nie są wymagane dla opublikowanej aplikacji, jeśli kompilacja Razor odbywa się w czasie kompilacji lub w czasie publikacji. Ustaw na `true` , jeśli opublikowana aplikacja wymaga kompilacji środowiska uruchomieniowego. Na przykład ustaw wartość na `true` Jeśli aplikacja modyfikuje pliki *. cshtml* w czasie wykonywania lub użyje widoków osadzonych. Wartość domyślna `false`to. |
| `IncludeRazorContentInPack` | Gdy `true`wszystkie elementy zawartości Razor (pliki *. cshtml* ) są oznaczone do uwzględnienia w wygenerowanym pakiecie NuGet. Wartość domyślna `false`to. |
| `EmbedRazorGenerateSources` | Gdy `true`program dodaje elementy RazorGenerate (*. cshtml*) jako osadzone pliki do wygenerowanego zestawu Razor. Wartość domyślna `false`to. |
| `UseRazorBuildServer` | Gdy `true`program używa procesu trwałego serwera kompilacji do odciążania operacji generowania kodu. Wartość domyślna to `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Gdy `true`zestaw SDK generuje dodatkowe atrybuty używane przez MVC w czasie wykonywania w celu przeprowadzenia odnajdywania części aplikacji. |
| `DefaultWebContentItemExcludes` | Wzorzec obsługi symboli wieloznacznych dla elementów elementów, które mają zostać wykluczone z grupy `Content` elementów w projektach przeznaczonych dla zestawu SDK sieci Web lub Razor |
| `ExcludeConfigFilesFromBuildOutput` | `true`Pliki *config* i *JSON* nie są kopiowane do katalogu wyjściowego kompilacji. |
| `AddRazorSupportForMvc` | Gdy `true`program SKONFIGURUJE zestaw SDK Razor w celu dodania obsługi konfiguracji MVC, która jest wymagana podczas kompilowania aplikacji zawierających widoki MVC lub Razor Pages. Ta właściwość jest niejawnie ustawiona dla projektów .NET Core 3,0 lub nowszych przeznaczonych dla zestawu SDK sieci Web |
| `RazorLangVersion` | Wersja języka Razor do celów. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Właściwość | Opis |
| -------- | ----------- |
| `RazorTargetName` | Nazwa pliku (bez rozszerzenia) zestawu utworzonego przez Razor. |
| `RazorOutputPath` | Katalog Razor wyjściowy. |
| `RazorCompileToolset` | Służy do określenia Razor zestawu narzędzi używanego do kompilowania zestawów. Prawidłowe wartości to `Implicit`, `RazorSDK`, i `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Wartość domyślna to `true`. Gdy `true`, zawiera pliki *Web. config*, *. JSON*i *. cshtml* jako zawartość projektu. Gdy jest przywoływany za pośrednictwem `Microsoft.NET.Sdk.Web`, uwzględniane są również pliki w folderze *wwwroot* i pliki konfiguracji. |
| `EnableDefaultRazorGenerateItems` | Gdy `true`, zawiera pliki *. cshtml* z `Content` elementów w `RazorGenerate` elementach. |
| `GenerateRazorTargetAssemblyInfo` | Gdy `true`program generuje plik *. cs* zawierający atrybuty określone przez `RazorAssemblyAttribute` i zawiera plik w danych wyjściowych kompilacji. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Gdy `true`program dodaje domyślny zestaw atrybutów zestawu do `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Gdy `true`kopiuje `RazorGenerate` pliki elementów (*. cshtml*) do katalogu publikowania. Zazwyczaj Razor pliki nie są wymagane dla opublikowanej aplikacji, jeśli uczestniczą w kompilacji w czasie kompilacji lub w czasie publikacji. Wartość domyślna `false`to. |
| `CopyRefAssembliesToPublishDirectory` | Gdy `true`Kopiuj elementy zestawu odwołań do katalogu publikowania. Zazwyczaj zestawy referencyjne nie są wymagane dla opublikowanej aplikacji Razor , jeśli kompilacja odbywa się podczas kompilacji lub czasu publikacji. Ustaw na `true` , jeśli opublikowana aplikacja wymaga kompilacji środowiska uruchomieniowego. Na przykład ustaw wartość na `true` Jeśli aplikacja modyfikuje pliki *. cshtml* w czasie wykonywania lub użyje widoków osadzonych. Wartość domyślna `false`to. |
| `IncludeRazorContentInPack` | Gdy `true`wszystkie Razor elementy zawartości (*. cshtml* ) są oznaczone do uwzględnienia w wygenerowanym pakiecie NuGet. Wartość domyślna `false`to. |
| `EmbedRazorGenerateSources` | Gdy `true`program dodaje elementy RazorGenerate (*. cshtml*) jako osadzone pliki do wygenerowanego Razor zestawu. Wartość domyślna `false`to. |
| `UseRazorBuildServer` | Gdy `true`program używa procesu trwałego serwera kompilacji do odciążania operacji generowania kodu. Wartość domyślna to `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Gdy `true`zestaw SDK generuje dodatkowe atrybuty używane przez MVC w czasie wykonywania w celu przeprowadzenia odnajdywania części aplikacji. |
| `DefaultWebContentItemExcludes` | Wzorzec obsługi symboli wieloznacznych dla elementów elementów, które mają być wykluczone z grupy `Content` elementów w projektach przeznaczonych dla sieci Web Razor lub zestawu SDK |
| `ExcludeConfigFilesFromBuildOutput` | `true`Pliki *config* i *JSON* nie są kopiowane do katalogu wyjściowego kompilacji. |
| `AddRazorSupportForMvc` | Gdy `true`program skonfiguruje Razor zestaw SDK w taki sposób, aby obsługiwał konfigurację MVC, która jest wymagana podczas kompilowania aplikacji Razor zawierających widoki lub strony MVC. Ta właściwość jest niejawnie ustawiona dla projektów .NET Core 3,0 lub nowszych przeznaczonych dla zestawu SDK sieci Web |
| `RazorLangVersion` | Wersja Razor języka do docelowa. |

::: moniker-end

Aby uzyskać więcej informacji na temat właściwości, zobacz [Właściwości programu MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Obiekty docelowe

Razor Zestaw SDK definiuje dwa podstawowe elementy docelowe:

* `RazorGenerate`&ndash; Kod generuje pliki *CS* z `RazorGenerate` elementów Item. Użyj `RazorGenerateDependsOn` właściwości, aby określić dodatkowe elementy docelowe, które mogą być uruchamiane przed lub po tym elemencie docelowym.
* `RazorCompile`&ndash; Kompiluje wygenerowane pliki *. cs* w Razor zestawie. Użyj, `RazorCompileDependsOn` aby określić dodatkowe elementy docelowe, które mogą być uruchamiane przed lub po tym elemencie docelowym.
* `RazorComponentGenerate`&ndash; Kod generuje pliki *CS* dla `RazorComponent` elementów Item. Użyj `RazorComponentGenerateDependsOn` właściwości, aby określić dodatkowe elementy docelowe, które mogą być uruchamiane przed lub po tym elemencie docelowym.

### <a name="runtime-compilation-of-razor-views"></a>Kompilacja Razor widoków środowiska uruchomieniowego

* Domyślnie Razor zestaw SDK nie publikuje zestawów referencyjnych, które są wymagane do wykonania kompilacji środowiska uruchomieniowego. Powoduje to błędy kompilacji, gdy model aplikacji opiera się na kompilacji&mdash;środowiska uruchomieniowego, na przykład aplikacja korzysta z widoków osadzonych lub zmian po opublikowaniu aplikacji. `true` Ustaw `CopyRefAssembliesToPublishDirectory` , aby kontynuować publikowanie zestawów odwołań.

* W przypadku aplikacji sieci Web upewnij się, że aplikacja jest `Microsoft.NET.Sdk.Web` ukierunkowana na zestaw SDK.

## <a name="razor-language-version"></a>Razorwersja językowa

W przypadku określania `Microsoft.NET.Sdk.Web` zestawu SDK wersja Razor językowa jest wywnioskowana z wersji platformy docelowej aplikacji. W przypadku projektów przeznaczonych dla `Microsoft.NET.Sdk.Razor` zestawu SDK lub w rzadkich przypadkach, gdy aplikacja Razor wymaga innej wersji językowej niż wnioskowana wartość, można skonfigurować wersję, ustawiając `<RazorLangVersion>` właściwość w pliku projektu aplikacji:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorwersja językowa jest ściśle zintegrowana z wersją środowiska uruchomieniowego, dla którego została skompilowana. Określanie wersji językowej, która nie jest przeznaczona dla środowiska uruchomieniowego, nie jest obsługiwane i prawdopodobnie powoduje błędy kompilacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatki do formatu csproj dla platformy .NET Core](/dotnet/core/tools/csproj)
* [Wspólne elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
