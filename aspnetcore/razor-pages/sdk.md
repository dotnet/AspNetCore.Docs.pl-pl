---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Dowiedz się, jak razor Pages w ASP.NET Core sprawia, że kodowanie scenariuszy skoncentrowanych na stronach jest łatwiejsze i bardziej wydajne niż przy użyciu MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80321012"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Omówienie

Zawiera [!INCLUDE[](~/includes/2.1-SDK.md)] `Microsoft.NET.Sdk.Razor` zestaw SDK MSBuild (Razor SDK). Brzytwa SDK:

::: moniker range=">= aspnetcore-3.0"

* Jest wymagane do tworzenia, pakowania i publikowania projektów zawierających pliki [Razor](xref:mvc/views/razor) dla ASP.NET core MVC lub [blazor](xref:blazor/index) projektów.
* Zawiera zestaw wstępnie zdefiniowanych obiektów docelowych, właściwości i elementów, które umożliwiają dostosowanie kompilacji plików Razor (*.cshtml* lub *.brzytwa).*

Zestaw Razor `Content` SDK `Include` zawiera elementy `**\*.cshtml` z `**\*.razor` atrybutami ustawionymi na wzorce globbingu. Pasujące pliki są publikowane.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standaryzuje środowisko związane z tworzeniem, pakowaniem i publikowaniem projektów zawierających pliki [Razor](xref:mvc/views/razor) dla projektów opartych na ASP.NET Core MVC.
* Zawiera zestaw wstępnie zdefiniowanych obiektów docelowych, właściwości i elementów, które umożliwiają dostosowywanie kompilacji plików Razor.

Zestaw Brzytwa zawiera `Content` element `Include` z atrybutem `**\*.cshtml` ustawionym na wzór globbingu. Pasujące pliki są publikowane.

::: moniker-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Korzystanie z SDK razor

Większość aplikacji internetowych nie jest wymagana do jawnego odwoływania się do SDK Razor.

::: moniker range=">= aspnetcore-3.0"

Aby używać razor SDK do tworzenia bibliotek klas zawierających widoki Razor lub Razor Pages, zaleca się rozpoczęcie od szablonu projektu biblioteki klas Razor (RCL). RCL, który jest używany do tworzenia plików Blazor *(.brzytwa*) minimalnie wymaga odwołania do pakietu [Microsoft.AspNetCore.Components.](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) RCL, który jest używany do tworzenia widoków razor lub stron (.cshtml plików) minimalnie wymaga *.cshtml* kierowania `netcoreapp3.0` lub później i ma `FrameworkReference` do [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) w pliku projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aby użyć SDK Razor do tworzenia bibliotek klas zawierających widoki Razor lub Strony Razor:

* Użyj `Microsoft.NET.Sdk.Razor` `Microsoft.NET.Sdk`zamiast:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Zazwyczaj odwołanie do `Microsoft.AspNetCore.Mvc` pakietu jest wymagane do odbierania dodatkowych zależności, które są wymagane do tworzenia i kompilowania stron Razor i widoki Razor. Co najmniej projekt powinien dodać odwołania do pakietu do:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Pakiet `Microsoft.AspNetCore.Razor.Design` zawiera zadania kompilacji Razor i cele dla projektu.

  Powyższe pakiety są `Microsoft.AspNetCore.Mvc`zawarte w . Następujące znaczniki przedstawia plik projektu, który używa razor SDK do tworzenia plików Razor dla aplikacji ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` Pakiety `Microsoft.AspNetCore.Mvc.Razor.Extensions` i pakiety są zawarte w [metapakietie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Jednak odwołanie do `Microsoft.AspNetCore.App` pakietu bez wersji zapewnia metapakiet do aplikacji, która `Microsoft.AspNetCore.Razor.Design`nie zawiera najnowszej wersji . Projekty muszą odwoływać `Microsoft.AspNetCore.Razor.Design` się `Microsoft.AspNetCore.Mvc`do spójnej wersji (lub), aby uwzględnić najnowsze poprawki czasu kompilacji dla Razor. Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Właściwości

Następujące właściwości kontrolują zachowanie SDK razor w ramach kompilacji projektu:

* `RazorCompileOnBuild`&ndash; Kiedy `true`, kompiluje i emituje zespół Razor w ramach tworzenia projektu. Wartość domyślna to `true`.
* `RazorCompileOnPublish`&ndash; Kiedy `true`, kompiluje i emituje razor zestawu w ramach publikowania projektu. Wartość domyślna to `true`.

Właściwości i elementy w poniższej tabeli są używane do konfigurowania wejść i danych wyjściowych do SDK Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Począwszy od ASP.NET Core 3.0, widoki MVC lub razor strony `RazorCompileOnBuild` nie `RazorCompileOnPublish` są obsługiwane domyślnie, jeśli lub MSBuild właściwości w pliku projektu są wyłączone. Aplikacje muszą dodać jawne odwołanie do pakietu [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation,](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) jeśli aplikacja opiera się na kompilacji środowiska uruchomieniowego do przetwarzania plików *cshtml.*

::: moniker-end

| Items | Opis |
| ----- | ----------- |
| `RazorGenerate` | Elementy elementu (pliki*cshtml),* które są wejściami do generowania kodu. |
| `RazorComponent` | Elementy elementu *(.razor* files), które są wejściami do generowania kodu komponentu Razor. |
| `RazorCompile` | Elementy elementu (pliki*cs),* które są wejściami do celów kompilacji Razor. Użyj `ItemGroup` tego, aby określić dodatkowe pliki, które mają być kompilowane do zestawu Razor. |
| `RazorTargetAssemblyAttribute` | Elementy towaru używane do kodować atrybuty generowania dla zestawu Razor. Przykład:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementy elementu dodane jako zasoby osadzone do wygenerowanego zestawu Razor. |

::: moniker range=">= aspnetcore-3.0"

| Właściwość | Opis |
| -------- | ----------- |
| `RazorTargetName` | Nazwa pliku (bez rozszerzenia) zestawu produkowanego przez Razor. |
| `RazorOutputPath` | Katalog wyjściowy Razor. |
| `RazorCompileToolset` | Służy do określania zestawu narzędzi używanego do tworzenia zespołu Razor. Prawidłowe `Implicit`wartości `RazorSDK`to `PrecompilationTool`, i . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Wartość domyślna to `true`. Kiedy `true`, zawiera *web.config*, *.json*i *.cshtml* plików jako zawartość w projekcie. W przypadku `Microsoft.NET.Sdk.Web`odwołania za pośrednictwem , pliki pod *wwwroot* i pliki konfiguracyjne są również zawarte. |
| `EnableDefaultRazorGenerateItems` | Kiedy `true`, zawiera pliki *.cshtml* z `Content` elementów w `RazorGenerate` elementach. |
| `GenerateRazorTargetAssemblyInfo` | Gdy `true`plik *.cs* zawiera atrybuty określone `RazorAssemblyAttribute` przez i zawiera plik w danych wyjściowych kompilacji. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Kiedy `true`, dodaje domyślny zestaw `RazorAssemblyAttribute`atrybutów złożenia do . |
| `CopyRazorGenerateFilesToPublishDirectory` | Kiedy `true`, `RazorGenerate` kopiuje elementy (*cshtml*) pliki do katalogu publikowania. Zazwyczaj pliki Razor nie są wymagane dla opublikowanej aplikacji, jeśli uczestniczą w kompilacji w czasie kompilacji lub czasie publikowania. Wartość domyślna to `false`. |
| `PreserveCompilationReferences` | Kiedy `true`, kopiuj elementy zestawu odwołań do katalogu publikowania. Zazwyczaj zestawy odwołań nie są wymagane dla opublikowanej aplikacji, jeśli kompilacja Razor występuje w czasie kompilacji lub czasie publikowania. Ustaw, `true` jeśli opublikowana aplikacja wymaga kompilacji środowiska uruchomieniowego. Na przykład ustaw wartość, `true` jeśli aplikacja modyfikuje pliki *.cshtml* w czasie wykonywania lub używa widoków osadzonych. Wartość domyślna to `false`. |
| `IncludeRazorContentInPack` | Gdy `true`wszystkie elementy zawartości Razor (pliki*cshtml)* są oznaczone do włączenia do wygenerowanego pakietu NuGet. Wartość domyślna to `false`. |
| `EmbedRazorGenerateSources` | Kiedy `true`, dodaje RazorGenerate (*.cshtml*) elementy jako osadzone pliki do wygenerowanego zestawu Razor. Wartość domyślna to `false`. |
| `UseRazorBuildServer` | Gdy `true`, używa trwałego procesu serwera kompilacji do odciążania pracy generowania kodu. Wartość domyślna wartości `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Gdy `true`, SDK generuje dodatkowe atrybuty używane przez MVC w czasie wykonywania do wykonywania odnajdowania części aplikacji. |
| `DefaultWebContentItemExcludes` | Wzorzec globbing dla elementów elementów, które mają być wykluczone z grupy `Content` elementów w projektach przeznaczonych dla sieci Web lub SDK Razor |
| `ExcludeConfigFilesFromBuildOutput` | Kiedy `true`pliki *.config* i *.json* nie są kopiowane do katalogu wyjściowego kompilacji. |
| `AddRazorSupportForMvc` | Gdy `true`, konfiguruje razor SDK, aby dodać obsługę konfiguracji MVC, która jest wymagana podczas tworzenia aplikacji zawierających widoki MVC lub Razor Pages. Ta właściwość jest niejawnie ustawiona dla projektów .NET Core 3.0 lub nowszych przeznaczonych dla zestawu SDK sieci Web |
| `RazorLangVersion` | Wersja języka razor do kierowania. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Właściwość | Opis |
| -------- | ----------- |
| `RazorTargetName` | Nazwa pliku (bez rozszerzenia) zestawu produkowanego przez Razor. |
| `RazorOutputPath` | Katalog wyjściowy Razor. |
| `RazorCompileToolset` | Służy do określania zestawu narzędzi używanego do tworzenia zespołu Razor. Prawidłowe `Implicit`wartości `RazorSDK`to `PrecompilationTool`, i . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Wartość domyślna to `true`. Kiedy `true`, zawiera *web.config*, *.json*i *.cshtml* plików jako zawartość w projekcie. W przypadku `Microsoft.NET.Sdk.Web`odwołania za pośrednictwem , pliki pod *wwwroot* i pliki konfiguracyjne są również zawarte. |
| `EnableDefaultRazorGenerateItems` | Kiedy `true`, zawiera pliki *.cshtml* z `Content` elementów w `RazorGenerate` elementach. |
| `GenerateRazorTargetAssemblyInfo` | Gdy `true`plik *.cs* zawiera atrybuty określone `RazorAssemblyAttribute` przez i zawiera plik w danych wyjściowych kompilacji. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Kiedy `true`, dodaje domyślny zestaw `RazorAssemblyAttribute`atrybutów złożenia do . |
| `CopyRazorGenerateFilesToPublishDirectory` | Kiedy `true`, `RazorGenerate` kopiuje elementy (*cshtml*) pliki do katalogu publikowania. Zazwyczaj pliki Razor nie są wymagane dla opublikowanej aplikacji, jeśli uczestniczą w kompilacji w czasie kompilacji lub czasie publikowania. Wartość domyślna to `false`. |
| `CopyRefAssembliesToPublishDirectory` | Kiedy `true`, kopiuj elementy zestawu odwołań do katalogu publikowania. Zazwyczaj zestawy odwołań nie są wymagane dla opublikowanej aplikacji, jeśli kompilacja Razor występuje w czasie kompilacji lub czasie publikowania. Ustaw, `true` jeśli opublikowana aplikacja wymaga kompilacji środowiska uruchomieniowego. Na przykład ustaw wartość, `true` jeśli aplikacja modyfikuje pliki *.cshtml* w czasie wykonywania lub używa widoków osadzonych. Wartość domyślna to `false`. |
| `IncludeRazorContentInPack` | Gdy `true`wszystkie elementy zawartości Razor (pliki*cshtml)* są oznaczone do włączenia do wygenerowanego pakietu NuGet. Wartość domyślna to `false`. |
| `EmbedRazorGenerateSources` | Kiedy `true`, dodaje RazorGenerate (*.cshtml*) elementy jako osadzone pliki do wygenerowanego zestawu Razor. Wartość domyślna to `false`. |
| `UseRazorBuildServer` | Gdy `true`, używa trwałego procesu serwera kompilacji do odciążania pracy generowania kodu. Wartość domyślna wartości `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Gdy `true`, SDK generuje dodatkowe atrybuty używane przez MVC w czasie wykonywania do wykonywania odnajdowania części aplikacji. |
| `DefaultWebContentItemExcludes` | Wzorzec globbing dla elementów elementów, które mają być wykluczone z grupy `Content` elementów w projektach przeznaczonych dla sieci Web lub SDK Razor |
| `ExcludeConfigFilesFromBuildOutput` | Kiedy `true`pliki *.config* i *.json* nie są kopiowane do katalogu wyjściowego kompilacji. |
| `AddRazorSupportForMvc` | Gdy `true`, konfiguruje razor SDK, aby dodać obsługę konfiguracji MVC, która jest wymagana podczas tworzenia aplikacji zawierających widoki MVC lub Razor Pages. Ta właściwość jest niejawnie ustawiona dla projektów .NET Core 3.0 lub nowszych przeznaczonych dla zestawu SDK sieci Web |
| `RazorLangVersion` | Wersja języka razor do kierowania. |

::: moniker-end

Aby uzyskać więcej informacji na temat właściwości, zobacz [MSBuild właściwości](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Obiekty docelowe

SDK Razor definiuje dwa podstawowe cele:

* `RazorGenerate`&ndash; Kod generuje pliki *.cs* z `RazorGenerate` elementów elementu. Użyj `RazorGenerateDependsOn` właściwości, aby określić dodatkowe obiekty docelowe, które można uruchomić przed lub po tym miejscu docelowym.
* `RazorCompile`&ndash; Kompiluje wygenerowane pliki *cs* do zestawu Razor. Użyj, `RazorCompileDependsOn` aby określić dodatkowe obiekty docelowe, które można uruchomić przed lub po tym miejscu docelowym.
* `RazorComponentGenerate`&ndash; Kod generuje *.cs* pliki cs `RazorComponent` dla elementów elementu. Użyj `RazorComponentGenerateDependsOn` właściwości, aby określić dodatkowe obiekty docelowe, które można uruchomić przed lub po tym miejscu docelowym.

### <a name="runtime-compilation-of-razor-views"></a>Kompilacja środowiska uruchomieniowego widoków Razor

* Domyślnie zestaw SDK Razor nie publikuje zestawów odwołań, które są wymagane do wykonywania kompilacji środowiska uruchomieniowego. Powoduje to błędy kompilacji, gdy model aplikacji opiera&mdash;się na kompilacji środowiska uruchomieniowego na przykład aplikacja używa widoków osadzonych lub zmienia widoki po opublikowaniu aplikacji. `CopyRefAssembliesToPublishDirectory` Ustaw, `true` aby kontynuować publikowanie zestawów odwołań.

* W przypadku aplikacji sieci web upewnij `Microsoft.NET.Sdk.Web` się, że aplikacja jest kierowana na sdk.

## <a name="razor-language-version"></a>Wersja językowa razor

Podczas kierowania `Microsoft.NET.Sdk.Web` na SDK, wersja języka Razor jest wywnioskowane z wersji struktury docelowej aplikacji. W przypadku projektów `Microsoft.NET.Sdk.Razor` przeznaczonych dla SDK lub w rzadkich przypadkach, gdy aplikacja wymaga innej wersji języka Razor `<RazorLangVersion>` niż wywnioskowana wartość, wersję można skonfigurować, ustawiając właściwość w pliku projektu aplikacji:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Wersja językowa Razor jest ściśle zintegrowana z wersją środowiska wykonawczego, dla których została zbudowana. Kierowanie na wersję językową, która nie jest przeznaczona dla środowiska wykonawczego, nie jest obsługiwane i prawdopodobnie powoduje błędy kompilacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatki do formatu csproj dla .NET Core](/dotnet/core/tools/csproj)
* [Typowe elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
