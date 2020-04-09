---
title: dotnet aspnet-codegenerator, polecenie
author: rick-anderson
description: Szkielety poleceń dotnet aspnet-codegenerator ASP.NET projektów core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665187"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`- Uruchamia silnik rusztowania ASP.NET Core. `dotnet aspnet-codegenerator`jest wymagane tylko do szkieletu z wiersza polecenia, nie jest potrzebne do używania szkieletów z programem Visual Studio.

Ten artykuł dotyczy [sdk .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) i nowszych.

## <a name="installing-aspnet-codegenerator"></a>Instalacja aspnet-codegenerator

`dotnet-aspnet-codegenerator`jest [globalnym narzędziem,](/dotnet/core/tools/global-tools) które musi być zainstalowane. Następujące polecenie instaluje najnowszą `dotnet-aspnet-codegenerator` stabilną wersję narzędzia:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Następujące polecenie `dotnet-aspnet-codegenerator` jest aktualizowane w najnowszej stabilnej wersji dostępnej w zainstalowanych pakietach SDK rdzenia .NET:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Streszczenie

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Opis

Globalne `dotnet aspnet-codegenerator` polecenie uruchamia generator kodu ASP.NET Core i silnik rusztowań.

## <a name="arguments"></a>Argumenty

`generator`

Generator kodu do uruchomienia. Dostępne są następujące generatory:

| Generator | Operacja |
| ----------------- | ------------ | 
| obszar      | [Rusztowania obszar](/aspnet/core/mvc/controllers/areas) |
  kontroler| [Rusztowania kontrolera](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identity  | [Tożsamość rusztowań](/aspnet/core/security/authentication/scaffold-identity) |
  brzytwa | [Rusztowania Razor Strony](/aspnet/core/tutorials/razor-pages/model) |
  widok      | [Rusztowania widoku](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>Opcje

`-n|--nuget-package-dir`

Określa katalog pakietu NuGet.

`-c|--configuration {Debug|Release}`

Definiuje konfigurację kompilacji. Wartością domyślną jest `Debug`.

`-tfm|--target-framework`

Struktura [docelowa](/dotnet/standard/frameworks) do użycia. Na przykład `net46`.

`-b|--build-base-path`

Ścieżka podstawowa kompilacji.

`-h|--help`

Drukuje krótką pomoc dla polecenia.

`--no-build`

Nie tworzy projektu przed uruchomieniem. Również niejawnie `--no-restore` ustawia flagę.

`-p|--project <PATH>`

Określa ścieżkę pliku projektu do uruchomienia (nazwę folderu lub pełną ścieżkę). Jeśli nie zostanie określony, domyślnie jest to bieżący katalog.

## <a name="generator-options"></a>Opcje generatora

W poniższych sekcjach wyszczególnia się opcjach dostępnych dla obsługiwanych generatorów:

* Obszar
* Kontrolera
* Tożsamość  
* Razorpage
* Widok

<a name="area"></a>

### <a name="area-options"></a>Opcje obszaru

To narzędzie jest przeznaczone do ASP.NET podstawowych projektów sieci web z kontrolerami i widokami. Nie jest przeznaczony dla aplikacji Razor Pages.

Użycie: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Poprzednie polecenie generuje następujące foldery:

* *Obszary*
  * *Nazwaogeneracji Obszaru*
    * *Kontrolery*
    * *Dane*
    * *Modele*
    * *Widoki*

<a name="ctl"></a>

### <a name="controller-options"></a>Opcje kontrolera

W poniższej tabeli wymieniono opcje dla: `aspnet-codegenerator` `controller` `razorpage`

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

W poniższej tabeli `aspnet-codegenerator controller`wymieniono opcje unikatowe dla:

| Opcja               | Opis|
| ----------------- | ------------ |
| --controllerName lub -name | Nazwa kontrolera. |
| --useAsyncAkcje lub -asynchroniczne | Generowanie akcji kontrolera asynchronii. |
| --noViews lub -nv | Nie **no** generuj żadnych widoków. |
| --restWithNoViews lub -api  | Generowanie kontrolera za pomocą interfejsu API stylu REST. `noViews`zakłada się, a wszystkie opcje związane z widokiem są ignorowane. |
| --readWypisań lub -akcji | Generowanie kontrolera z akcjami odczytu/zapisu bez modelu. |

Użyj `-h` przełącznika, aby `aspnet-codegenerator controller` uzyskać pomoc dotyczącą polecenia:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Zobacz [Scaffold modelu filmu](/aspnet/core/tutorials/razor-pages/model) na `dotnet aspnet-codegenerator controller`przykład .

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

Strony razor mogą być indywidualnie rusztowania, określając nazwę nowej strony i szablon do użycia. Obsługiwane szablony to:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Na przykład następujące polecenie używa szablonu Edytuj do wygenerowania *pliku MyEdit.cshtml* i *MyEdit.cshtml.cs:*

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Zazwyczaj nie określono szablonu i wygenerowanej nazwy pliku i tworzone są następujące szablony:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

W poniższej tabeli wymieniono opcje dla: `aspnet-codegenerator` `razorpage` `controller`

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

W poniższej tabeli `aspnet-codegenerator razorpage`wymieniono opcje unikatowe dla:

| Opcja               | Opis|
| ----------------- | ------------ |
|   --namespaceName lub -obszar nazw | Nazwa obszaru nazw używanego dla wygenerowanego programu PageModel |
| --partialView lub -częściowe | Generowanie widoku częściowego. Opcje układu -l i -udl są ignorowane, jeśli jest to określone. |
| --noPageModel lub -npm | Przełączanie, aby nie generować klasy PageModel dla pustego szablonu |

Użyj `-h` przełącznika, aby `aspnet-codegenerator razorpage` uzyskać pomoc dotyczącą polecenia:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Zobacz [Scaffold modelu filmu](/aspnet/core/tutorials/razor-pages/model) na `dotnet aspnet-codegenerator razorpage`przykład .

### <a name="identity"></a>Tożsamość

Zobacz [Tożsamość rusztowania](/aspnet/core/security/authentication/scaffold-identity)
