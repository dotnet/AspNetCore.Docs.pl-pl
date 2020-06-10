---
title: RazorZestaw SDK ASP.NET Core
author: Rick-Anderson
description: Dowiedz się Razor , jak strony w ASP.NET Core sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie MVC.
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
ms.openlocfilehash: 555965b96b0d4d541c70f8a137d4f098dc06a4d6
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106627"
---
# <a name="aspnet-core-razor-sdk"></a>RazorZestaw SDK ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Omówienie

[!INCLUDE[](~/includes/2.1-SDK.md)]Zawiera zestaw SDK programu `Microsoft.NET.Sdk.Razor` MSBuild ( Razor SDK). RazorZestaw SDK:

::: moniker range=">= aspnetcore-3.0"

* Jest wymagana do kompilowania, pakowania i publikowania projektów zawierających [Razor](xref:mvc/views/razor) pliki do ASP.NET Core MVC lub [Blazor](xref:blazor/index) projektów.
* Zawiera zestaw wstępnie zdefiniowanych obiektów docelowych, właściwości i elementów umożliwiających Dostosowywanie kompilacji Razor plików (*. cshtml* lub *. Razor*).

RazorZestaw SDK zawiera `Content` elementy z `Include` atrybutami ustawionymi `**\*.cshtml` na `**\*.razor` wzorce i obsługi symboli wieloznacznych. Zgodne pliki są publikowane.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standaryzacja środowiska tworzenia, pakowania i publikowania projektów zawierających [Razor](xref:mvc/views/razor) pliki dla ASP.NET Core projektów opartych na MVC.
* Zawiera zestaw wstępnie zdefiniowanych obiektów docelowych, właściwości i elementów umożliwiających Dostosowywanie kompilacji Razor plików.

RazorZestaw SDK zawiera `Content` element z `Include` atrybutem ustawionym na `**\*.cshtml` wzorzec obsługi symboli wieloznacznych. Zgodne pliki są publikowane.

::: moniker-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Korzystanie z Razor zestawu SDK

Większość aplikacji sieci Web nie jest wymagana do jawnego odwoływania się do Razor zestawu SDK.

::: moniker range=">= aspnetcore-3.0"

Aby użyć Razor zestawu SDK do kompilowania bibliotek klas zawierających Razor widoki lub Razor strony, zalecamy rozpoczęcie od Razor szablonu projektu Biblioteka klas (RCL). RCL, który jest używany do kompilowania Blazor plików (*. Razor*) w minimalnym stopniu wymaga odwołania do pakietu [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . RCL, który jest używany do kompilowania Razor widoków lub stron (pliki *. cshtml* ), wymaga minimalnej `netcoreapp3.0` lub nowszej, a `FrameworkReference` w pliku projektu znajduje się w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aby użyć Razor zestawu SDK do kompilowania bibliotek klas zawierających Razor widoki lub Razor strony:

* Użyj `Microsoft.NET.Sdk.Razor` zamiast `Microsoft.NET.Sdk` :

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Zazwyczaj odwołanie do pakietu `Microsoft.AspNetCore.Mvc` jest wymagane do uzyskania dodatkowych zależności, które są wymagane do kompilowania i kompilowania Razor stron i Razor widoków. Co najmniej, projekt powinien dodawać odwołania do pakietów do:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design`Pakiet zawiera Razor zadania kompilacji i elementy docelowe dla projektu.

  Poprzednie pakiety są zawarte w `Microsoft.AspNetCore.Mvc` . Poniższe znaczniki przedstawiają plik projektu, który używa Razor zestawu SDK do kompilowania Razor plików dla Razor aplikacji ASP.NET Core strony:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`Pakiety i `Microsoft.AspNetCore.Mvc.Razor.Extensions` są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Niemniej jednak `Microsoft.AspNetCore.App` Dokumentacja pakietu bez wersji zapewnia pakiet dla aplikacji, która nie zawiera najnowszej wersji programu `Microsoft.AspNetCore.Razor.Design` . Projekty muszą odwoływać się do spójnej wersji `Microsoft.AspNetCore.Razor.Design` (lub `Microsoft.AspNetCore.Mvc` ) w celu uwzględnienia najnowszych poprawek czasu kompilacji dla programu Razor . Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/aspnet/Razor/issues/2553)w serwisie GitHub.

::: moniker-end

### <a name="properties"></a>Właściwości

Następujące właściwości sterują Razor zachowaniem zestawu SDK w ramach kompilacji projektu:

* `RazorCompileOnBuild`: Kiedy `true` , kompiluje i emituje Razor zestaw w ramach kompilowania projektu. Wartość domyślna to `true` .
* `RazorCompileOnPublish`: Kiedy `true` , kompiluje i emituje Razor zestaw w ramach publikowania projektu. Wartość domyślna to `true` .

Właściwości i elementy w poniższej tabeli służą do konfigurowania danych wejściowych i wyjściowych do Razor zestawu SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Począwszy od ASP.NET Core 3,0, widoki i Razor strony MVC nie są obsługiwane domyślnie, jeśli `RazorCompileOnBuild` Właściwości programu `RazorCompileOnPublish` MSBuild w pliku projektu są wyłączone. Aplikacje muszą dodać jawne odwołanie do obiektu [Microsoft. AspNetCore. MVC. Razor . Pakiet RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , jeśli aplikacja korzysta z kompilacji środowiska uruchomieniowego w celu przetworzenia plików *. cshtml* .

::: moniker-end

| Items (Elementy) | Opis |
| ----- | ----------- |
| `RazorGenerate` | Elementy elementu (pliki *. cshtml* ), które są danymi wejściowymi do generowania kodu. |
| `RazorComponent` | Elementy elementu (pliki*Razor* ), które są danymi wejściowymi Razor generowania kodu składnika. |
| `RazorCompile` | Elementy elementów (pliki*CS* ), które są danymi wejściowymi do Razor elementów docelowych kompilacji. Użyj tego `ItemGroup` , aby określić dodatkowe pliki do skompilowania do Razor zestawu. |
| `RazorTargetAssemblyAttribute` | Elementy elementów używane do tworzenia kodu atrybutów dla Razor zestawu. Przykład:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementy elementu dodane jako zasoby osadzone do wygenerowanego Razor zestawu. |

::: moniker range=">= aspnetcore-3.0"

| Właściwość | Opis |
| -------- | ----------- |
| `RazorTargetName` | Nazwa pliku (bez rozszerzenia) zestawu utworzonego przez Razor . |
| `RazorOutputPath` | RazorKatalog wyjściowy. |
| `RazorCompileToolset` | Służy do określenia zestawu narzędzi używanego do kompilowania Razor zestawów. Prawidłowe wartości to `Implicit` , `RazorSDK` , i `PrecompilationTool` . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Wartość domyślna to `true`. Gdy `true` , zawiera pliki *Web. config*, *. JSON*i *. cshtml* jako zawartość projektu. Gdy jest przywoływany za pośrednictwem `Microsoft.NET.Sdk.Web` , uwzględniane są również pliki w folderze *wwwroot* i pliki konfiguracji. |
| `EnableDefaultRazorGenerateItems` | Gdy `true` , zawiera pliki *. cshtml* z `Content` elementów w `RazorGenerate` elementach. |
| `GenerateRazorTargetAssemblyInfo` | Gdy `true` program generuje plik *. cs* zawierający atrybuty określone przez `RazorAssemblyAttribute` i zawiera plik w danych wyjściowych kompilacji. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Gdy `true` program dodaje domyślny zestaw atrybutów zestawu do `RazorAssemblyAttribute` . |
| `CopyRazorGenerateFilesToPublishDirectory` | Gdy `true` kopiuje `RazorGenerate` pliki elementów (*. cshtml*) do katalogu publikowania. Zazwyczaj Razor pliki nie są wymagane dla opublikowanej aplikacji, jeśli uczestniczą w kompilacji w czasie kompilacji lub w czasie publikacji. Wartość domyślna to `false` . |
| `PreserveCompilationReferences` | Gdy `true` Kopiuj elementy zestawu odwołań do katalogu publikowania. Zazwyczaj zestawy referencyjne nie są wymagane dla opublikowanej aplikacji, jeśli Razor kompilacja odbywa się podczas kompilacji lub czasu publikacji. Ustaw na, `true` Jeśli opublikowana aplikacja wymaga kompilacji środowiska uruchomieniowego. Na przykład ustaw wartość na `true` Jeśli aplikacja modyfikuje pliki *. cshtml* w czasie wykonywania lub użyje widoków osadzonych. Wartość domyślna to `false` . |
| `IncludeRazorContentInPack` | Gdy `true` wszystkie Razor elementy zawartości (*. cshtml* ) są oznaczone do uwzględnienia w wygenerowanym pakiecie NuGet. Wartość domyślna to `false` . |
| `EmbedRazorGenerateSources` | Gdy `true` program dodaje elementy RazorGenerate (*. cshtml*) jako osadzone pliki do wygenerowanego Razor zestawu. Wartość domyślna to `false` . |
| `UseRazorBuildServer` | Gdy `true` program używa procesu trwałego serwera kompilacji do odciążania operacji generowania kodu. Wartość domyślna to `UseSharedCompilation` . |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Gdy `true` zestaw SDK generuje dodatkowe atrybuty używane przez MVC w czasie wykonywania w celu przeprowadzenia odnajdywania części aplikacji. |
| `DefaultWebContentItemExcludes` | Wzorzec obsługi symboli wieloznacznych dla elementów elementów, które mają być wykluczone z `Content` grupy elementów w projektach przeznaczonych dla sieci Web lub Razor zestawu SDK |
| `ExcludeConfigFilesFromBuildOutput` | `true`Pliki *config* i *JSON* nie są kopiowane do katalogu wyjściowego kompilacji. |
| `AddRazorSupportForMvc` | Gdy `true` program skonfiguruje Razor zestaw SDK w taki sposób, aby OBSŁUGIWAŁ konfigurację MVC, która jest wymagana podczas kompilowania aplikacji zawierających widoki lub Razor strony MVC. Ta właściwość jest niejawnie ustawiona dla projektów .NET Core 3,0 lub nowszych przeznaczonych dla zestawu SDK sieci Web |
| `RazorLangVersion` | Wersja Razor języka do docelowa. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Właściwość | Opis |
| -------- | ----------- |
| `RazorTargetName` | Nazwa pliku (bez rozszerzenia) zestawu utworzonego przez Razor . |
| `RazorOutputPath` | RazorKatalog wyjściowy. |
| `RazorCompileToolset` | Służy do określenia zestawu narzędzi używanego do kompilowania Razor zestawów. Prawidłowe wartości to `Implicit` , `RazorSDK` , i `PrecompilationTool` . |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Wartość domyślna to `true`. Gdy `true` , zawiera pliki *Web. config*, *. JSON*i *. cshtml* jako zawartość projektu. Gdy jest przywoływany za pośrednictwem `Microsoft.NET.Sdk.Web` , uwzględniane są również pliki w folderze *wwwroot* i pliki konfiguracji. |
| `EnableDefaultRazorGenerateItems` | Gdy `true` , zawiera pliki *. cshtml* z `Content` elementów w `RazorGenerate` elementach. |
| `GenerateRazorTargetAssemblyInfo` | Gdy `true` program generuje plik *. cs* zawierający atrybuty określone przez `RazorAssemblyAttribute` i zawiera plik w danych wyjściowych kompilacji. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Gdy `true` program dodaje domyślny zestaw atrybutów zestawu do `RazorAssemblyAttribute` . |
| `CopyRazorGenerateFilesToPublishDirectory` | Gdy `true` kopiuje `RazorGenerate` pliki elementów (*. cshtml*) do katalogu publikowania. Zazwyczaj Razor pliki nie są wymagane dla opublikowanej aplikacji, jeśli uczestniczą w kompilacji w czasie kompilacji lub w czasie publikacji. Wartość domyślna to `false` . |
| `CopyRefAssembliesToPublishDirectory` | Gdy `true` Kopiuj elementy zestawu odwołań do katalogu publikowania. Zazwyczaj zestawy referencyjne nie są wymagane dla opublikowanej aplikacji, jeśli Razor kompilacja odbywa się podczas kompilacji lub czasu publikacji. Ustaw na, `true` Jeśli opublikowana aplikacja wymaga kompilacji środowiska uruchomieniowego. Na przykład ustaw wartość na `true` Jeśli aplikacja modyfikuje pliki *. cshtml* w czasie wykonywania lub użyje widoków osadzonych. Wartość domyślna to `false` . |
| `IncludeRazorContentInPack` | Gdy `true` wszystkie Razor elementy zawartości (*. cshtml* ) są oznaczone do uwzględnienia w wygenerowanym pakiecie NuGet. Wartość domyślna to `false` . |
| `EmbedRazorGenerateSources` | Gdy `true` program dodaje elementy RazorGenerate (*. cshtml*) jako osadzone pliki do wygenerowanego Razor zestawu. Wartość domyślna to `false` . |
| `UseRazorBuildServer` | Gdy `true` program używa procesu trwałego serwera kompilacji do odciążania operacji generowania kodu. Wartość domyślna to `UseSharedCompilation` . |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Gdy `true` zestaw SDK generuje dodatkowe atrybuty używane przez MVC w czasie wykonywania w celu przeprowadzenia odnajdywania części aplikacji. |
| `DefaultWebContentItemExcludes` | Wzorzec obsługi symboli wieloznacznych dla elementów elementów, które mają być wykluczone z `Content` grupy elementów w projektach przeznaczonych dla sieci Web lub Razor zestawu SDK |
| `ExcludeConfigFilesFromBuildOutput` | `true`Pliki *config* i *JSON* nie są kopiowane do katalogu wyjściowego kompilacji. |
| `AddRazorSupportForMvc` | Gdy `true` program skonfiguruje Razor zestaw SDK w taki sposób, aby OBSŁUGIWAŁ konfigurację MVC, która jest wymagana podczas kompilowania aplikacji zawierających widoki lub Razor strony MVC. Ta właściwość jest niejawnie ustawiona dla projektów .NET Core 3,0 lub nowszych przeznaczonych dla zestawu SDK sieci Web |
| `RazorLangVersion` | Wersja Razor języka do docelowa. |

::: moniker-end

Aby uzyskać więcej informacji na temat właściwości, zobacz [Właściwości programu MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Obiekty docelowe

RazorZestaw SDK definiuje dwa podstawowe elementy docelowe:

* `RazorGenerate`: Kod generuje pliki *CS* z `RazorGenerate` elementów Item. Użyj `RazorGenerateDependsOn` właściwości, aby określić dodatkowe elementy docelowe, które mogą być uruchamiane przed lub po tym elemencie docelowym.
* `RazorCompile`: Kompiluje wygenerowane pliki *. cs* w Razor zestawie. Użyj, `RazorCompileDependsOn` Aby określić dodatkowe elementy docelowe, które mogą być uruchamiane przed lub po tym elemencie docelowym.
* `RazorComponentGenerate`: Kod generuje pliki *CS* dla `RazorComponent` elementów Item. Użyj `RazorComponentGenerateDependsOn` właściwości, aby określić dodatkowe elementy docelowe, które mogą być uruchamiane przed lub po tym elemencie docelowym.

### <a name="runtime-compilation-of-razor-views"></a>Kompilacja widoków środowiska uruchomieniowego Razor

* Domyślnie Razor zestaw SDK nie publikuje zestawów referencyjnych, które są wymagane do wykonania kompilacji środowiska uruchomieniowego. Powoduje to błędy kompilacji, gdy model aplikacji opiera się na kompilacji środowiska uruchomieniowego &mdash; , na przykład aplikacja korzysta z widoków osadzonych lub zmian po opublikowaniu aplikacji. Ustaw, aby `CopyRefAssembliesToPublishDirectory` `true` kontynuować publikowanie zestawów odwołań.

* W przypadku aplikacji sieci Web upewnij się, że aplikacja jest ukierunkowana na `Microsoft.NET.Sdk.Web` zestaw SDK.

## <a name="razor-language-version"></a>Razorwersja językowa

W przypadku określania `Microsoft.NET.Sdk.Web` zestawu SDK Razor wersja językowa jest wywnioskowana z wersji platformy docelowej aplikacji. W przypadku projektów przeznaczonych dla `Microsoft.NET.Sdk.Razor` zestawu SDK lub w rzadkich przypadkach, gdy aplikacja wymaga innej Razor wersji językowej niż wnioskowana wartość, można skonfigurować wersję, ustawiając `<RazorLangVersion>` Właściwość w pliku projektu aplikacji:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorwersja językowa jest ściśle zintegrowana z wersją środowiska uruchomieniowego, dla którego została skompilowana. Określanie wersji językowej, która nie jest przeznaczona dla środowiska uruchomieniowego, nie jest obsługiwane i prawdopodobnie powoduje błędy kompilacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dodatki do formatu csproj dla platformy .NET Core](/dotnet/core/tools/csproj)
* [Wspólne elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
