---
title: ASP.NET Podstawowa struktura katalogów
author: rick-anderson
description: Dowiedz się więcej o strukturze katalogów opublikowanych aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 3ed27db31c5fda8376ed108568ea74b657d30531
ms.sourcegitcommit: 4506a8f71ece921010ad6b7edebc8b200618f40d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81002941"
---
# <a name="aspnet-core-directory-structure"></a>ASP.NET Podstawowa struktura katalogów

::: moniker range=">= aspnetcore-3.0"

Katalog *publikowania* zawiera zasoby, które można wdrożyć w aplikacji, wyprodukowane przez polecenie [publikowania dotnet.](/dotnet/core/tools/dotnet-publish) Katalog zawiera:

* Pliki aplikacji
* Pliki konfiguracyjne
* Aktywa statyczne
* Pakiety
* Środowisko wykonawcze (tylko[samodzielne wdrożenie)](/dotnet/core/deploying/#self-contained-deployments-scd)

| Typ aplikacji | Struktura katalogów |
| -------- | ------------------- |
| [Plik wykonywalny zależny od struktury (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>Publikowania&dagger;<ul><li>Widoki&dagger; aplikacji MVC; jeśli widoki nie są wstępnie kompilowane</li><li>Strony&dagger; Aplikacje MVC lub Razor Pages, jeśli strony nie są wstępnie kompilowane</li><li>wwwroot&dagger;</li><li>\*pliki dll</li><li>{NAZWA ZESTAWU}.deps.json</li><li>{NAZWA ZESTAWU}.dll</li><li>{NAZWA ZESTAWU} {. EXTENSION} .exe extension on Windows, no extension on macOS or Linux</li><li>{NAZWA ZESTAWU}.pdb</li><li>{NAZWA ZESTAWU}. Plik Views.dll</li><li>{NAZWA ZESTAWU}. Widok.pdb</li><li>{NAZWA ZESTAWU}.runtimeconfig.json</li><li>web.config (wdrożenia usług IIS)</li><li>createdump ([Linux createdump narzędzie](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (biblioteka obiektów współdzielonych systemu Linux)</li><li>\*.a (archiwum macOS)</li><li>\*.dylib (biblioteka dynamiczna macOS)</li></ul></li></ul> |
| [Samodzielne wdrażanie (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Publikowania&dagger;<ul><li>Wyświetla&dagger; aplikacje MVC, jeśli widoki nie są wstępnie kompilowane</li><li>Strony&dagger; Aplikacje MVC lub Razor Pages, jeśli strony nie są wstępnie kompilowane</li><li>wwwroot&dagger;</li><li>\*pliki dll</li><li>{NAZWA ZESTAWU}.deps.json</li><li>{NAZWA ZESTAWU}.dll</li><li>{NAZWA ZESTAWU}.exe</li><li>{NAZWA ZESTAWU}.pdb</li><li>{NAZWA ZESTAWU}. Plik Views.dll</li><li>{NAZWA ZESTAWU}. Widok.pdb</li><li>{NAZWA ZESTAWU}.runtimeconfig.json</li><li>web.config (wdrożenia usług IIS)</li></ul></li></ul> |

&dagger;Wskazuje katalog

Katalog *publikowania* reprezentuje *ścieżkę katalogu głównego zawartości*, zwaną również *ścieżką podstawową aplikacji,* wdrożenia. Niezależnie od nazwy nadanej katalogu *publikowania* wdrożonej aplikacji na serwerze, jego lokalizacja służy jako fizyczna ścieżka serwera do hosta aplikacji.

Katalog *wwwroot,* jeśli jest obecny, zawiera tylko zasoby statyczne.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Wdrożenie aplikacji .NET Core](/dotnet/core/deploying/)
* [Platformy docelowe](/dotnet/standard/frameworks)
* [Katalog RID rdzenia .NET](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Katalog *publikowania* zawiera zasoby, które można wdrożyć w aplikacji, wyprodukowane przez polecenie [publikowania dotnet.](/dotnet/core/tools/dotnet-publish) Katalog zawiera:

* Pliki aplikacji
* Pliki konfiguracyjne
* Aktywa statyczne
* Pakiety
* Środowisko wykonawcze (tylko[samodzielne wdrożenie)](/dotnet/core/deploying/#self-contained-deployments-scd)

| Typ aplikacji | Struktura katalogów |
| -------- | ------------------- |
| [Plik wykonywalny zależny od struktury (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>Publikowania&dagger;<ul><li>Widoki&dagger; aplikacji MVC; jeśli widoki nie są wstępnie kompilowane</li><li>Strony&dagger; Aplikacje MVC lub Razor Pages, jeśli strony nie są wstępnie kompilowane</li><li>wwwroot&dagger;</li><li>\*pliki dll</li><li>{NAZWA ZESTAWU}.deps.json</li><li>{NAZWA ZESTAWU}.dll</li><li>{NAZWA ZESTAWU} {. EXTENSION} .exe extension on Windows, no extension on macOS or Linux</li><li>{NAZWA ZESTAWU}.pdb</li><li>{NAZWA ZESTAWU}. Plik Views.dll</li><li>{NAZWA ZESTAWU}. Widok.pdb</li><li>{NAZWA ZESTAWU}.runtimeconfig.json</li><li>web.config (wdrożenia usług IIS)</li><li>createdump ([Linux createdump narzędzie](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (biblioteka obiektów współdzielonych systemu Linux)</li><li>\*.a (archiwum macOS)</li><li>\*.dylib (biblioteka dynamiczna macOS)</li></ul></li></ul> |
| [Samodzielne wdrażanie (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Publikowania&dagger;<ul><li>Wyświetla&dagger; aplikacje MVC, jeśli widoki nie są wstępnie kompilowane</li><li>Strony&dagger; Aplikacje MVC lub Razor Pages, jeśli strony nie są wstępnie kompilowane</li><li>wwwroot&dagger;</li><li>\*pliki dll</li><li>{NAZWA ZESTAWU}.deps.json</li><li>{NAZWA ZESTAWU}.dll</li><li>{NAZWA ZESTAWU}.exe</li><li>{NAZWA ZESTAWU}.pdb</li><li>{NAZWA ZESTAWU}. Plik Views.dll</li><li>{NAZWA ZESTAWU}. Widok.pdb</li><li>{NAZWA ZESTAWU}.runtimeconfig.json</li><li>web.config (wdrożenia usług IIS)</li></ul></li></ul> |

&dagger;Wskazuje katalog

Katalog *publikowania* reprezentuje *ścieżkę katalogu głównego zawartości*, zwaną również *ścieżką podstawową aplikacji,* wdrożenia. Niezależnie od nazwy nadanej katalogu *publikowania* wdrożonej aplikacji na serwerze, jego lokalizacja służy jako fizyczna ścieżka serwera do hosta aplikacji.

Katalog *wwwroot,* jeśli jest obecny, zawiera tylko zasoby statyczne.

Tworzenie folderu *Dzienniki* jest przydatne do [ASP.NET modułu rdzenia rozszerzonego rejestrowania debugowania](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Foldery w ścieżce `<handlerSetting>` dostarczonej do wartości nie są tworzone przez moduł automatycznie i powinny wstępnie istnieć we wdrożeniu, aby umożliwić modułowi zapisanie dziennika debugowania.

Katalog *Dzienniki* można utworzyć dla wdrożenia przy użyciu jednego z następujących dwóch metod:

* Dodaj następujący `<Target>` element do pliku projektu:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   Element `<MakeDir>` tworzy pusty folder *Dzienniki* w opublikowanym danych wyjściowych. Element używa właściwości `PublishDir` do określenia lokalizacji docelowej do tworzenia folderu. Kilka metod wdrażania, takich jak Wdrażanie w sieci Web, pomija puste foldery podczas wdrażania. Element `<WriteLinesToFile>` generuje plik w folderze *Dzienniki,* co gwarantuje wdrożenie folderu na serwerze. Tworzenie folderów przy użyciu tej metody kończy się niepowodzeniem, jeśli proces roboczy nie ma dostępu do zapisu do folderu docelowego.

* Fizycznie utwórz katalog *Dzienniki* na serwerze we wdrożeniu.

Katalog wdrażania wymaga uprawnień Odczyt/Wykonanie. Katalog *Dzienniki* wymaga uprawnień odczytu/zapisu. Dodatkowe katalogi, w których są zapisywane pliki, wymagają uprawnień do odczytu/zapisu.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Wdrożenie aplikacji .NET Core](/dotnet/core/deploying/)
* [Platformy docelowe](/dotnet/standard/frameworks)
* [Katalog RID rdzenia .NET](/dotnet/core/rid-catalog)

::: moniker-end
