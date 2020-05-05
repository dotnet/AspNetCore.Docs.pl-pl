---
title: dotnet ASPNET-CodeGenerator polecenie
author: rick-anderson
description: ASP.NET Core projekty są szkieletami poleceń dotnet ASPNET-CodeGenerator.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 58f7aa30d3e916307437d56c61e80765ac0c21cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766475"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet ASPNET-CodeGenerator

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`-Uruchamia aparat tworzenia szkieletów ASP.NET Core. `dotnet aspnet-codegenerator`jest wymagany tylko dla szkieletu z wiersza polecenia, nie jest potrzebny do korzystania z szkieletów w programie Visual Studio.

Ten artykuł dotyczy [zestawu .NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) i nowszych wersji.

## <a name="installing-aspnet-codegenerator"></a>Instalowanie ASPNET-CodeGenerator

`dotnet-aspnet-codegenerator`jest [globalnym narzędziem](/dotnet/core/tools/global-tools) , które musi być zainstalowane. Następujące polecenie instaluje najnowszą stabilną wersję `dotnet-aspnet-codegenerator` narzędzia:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Następujące polecenie aktualizuje `dotnet-aspnet-codegenerator` najnowszą stabilną wersję dostępną z zainstalowanych zestawów SDK platformy .NET Core:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Streszczenie

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Opis

Polecenie `dotnet aspnet-codegenerator` globalne uruchamia ASP.NET Core generatora kodu i aparacie tworzenia szkieletów.

## <a name="arguments"></a>Argumenty

`generator`

Generator kodu do uruchomienia. Dostępne są następujące generatory:

| Generator | Operacja |
| ----------------- | ------------ | 
| obszar      | [Szkieletuje obszar](/aspnet/core/mvc/controllers/areas) |
  kontroler| [Tworzy szkielety kontrolera](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identity  | [Tożsamość szkieletów](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Razor Pages szkieletów](/aspnet/core/tutorials/razor-pages/model) |
  widok      | [Tworzy szkielety widoku](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>Opcje

`-n|--nuget-package-dir`

Określa katalog pakietu NuGet.

`-c|--configuration {Debug|Release}`

Definiuje konfigurację kompilacji. Wartość domyślna to `Debug`.

`-tfm|--target-framework`

[Platforma](/dotnet/standard/frameworks) docelowa do użycia. Na przykład `net46`.

`-b|--build-base-path`

Ścieżka bazowa kompilacji.

`-h|--help`

Drukuje krótką pomoc dla polecenia.

`--no-build`

Nie kompiluje projektu przed uruchomieniem. Również niejawnie ustawia `--no-restore` flagę.

`-p|--project <PATH>`

Określa ścieżkę do pliku projektu do uruchomienia (nazwa folderu lub pełna ścieżka). Jeśli nie zostanie określony, domyślnie jest bieżącym katalogiem.

## <a name="generator-options"></a>Opcje generatora

Poniższe sekcje zawierają szczegółowe informacje o opcjach dostępnych dla obsługiwanych generatorów:

* Obszar
* Kontroler
* Tożsamość  
* Razorpage
* Widok

<a name="area"></a>

### <a name="area-options"></a>Opcje obszaru

To narzędzie jest przeznaczone do ASP.NET Core projektów sieci Web z kontrolerami i widokami. Nie jest ona przeznaczona do Razor Pages aplikacji.

Użycie: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Poprzednie polecenie generuje następujące foldery:

* *Obszary*
  * *AreaNameToGenerate*
    * *Kontrolery*
    * *Dane*
    * *Modele*
    * *Widoki*

<a name="ctl"></a>

### <a name="controller-options"></a>Opcje kontrolera

W poniższej tabeli wymieniono opcje `aspnet-codegenerator` `controller` i `razorpage`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

W poniższej tabeli wymieniono opcje unikatowe `aspnet-codegenerator controller`dla:

| Opcja               | Opis|
| ----------------- | ------------ |
| --ControllerName lub-Name | Nazwa kontrolera. |
| --useAsyncActions lub-async | Generuj akcje kontrolerów asynchronicznych. |
| --noviews lub NV | **Nie Generuj żadnych** widoków. |
| --restWithNoViews lub-API  | Wygeneruj kontroler z interfejsem API REST. `noViews`Założono, że wszystkie opcje powiązane z widokiem są ignorowane. |
| --readWriteActions lub-akcje | Generuj kontroler z akcjami odczytu/zapisu bez modelu. |

Użyj przełącznika `-h` , aby uzyskać pomoc dotyczącą `aspnet-codegenerator controller` polecenia:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Aby zapoznać się z `dotnet aspnet-codegenerator controller`przykładem, zobacz Tworzenie [szkieletu modelu filmu](/aspnet/core/tutorials/razor-pages/model) .

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

Razor Pages mogą być tworzone indywidualnie przez określenie nazwy nowej strony i szablonu do użycia. Obsługiwane są następujące szablony:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Na przykład następujące polecenie używa polecenia Edytuj szablon do wygenerowania elementu *MyEdit.cshtml.cs* *. cshtml* i:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Zazwyczaj nazwa szablonu i wygenerowanego pliku nie jest określona i tworzone są następujące szablony:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

W poniższej tabeli wymieniono opcje `aspnet-codegenerator` `razorpage` i `controller`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

W poniższej tabeli wymieniono opcje unikatowe `aspnet-codegenerator razorpage`dla:

| Opcja               | Opis|
| ----------------- | ------------ |
|   --NamespaceName lub-Namespace | Nazwa przestrzeni nazw do użycia dla wygenerowanego PageModel |
| --partialView lub-częściowe | Generuj widok częściowy. Opcje układu-l i-UDL są ignorowane, jeśli jest określony. |
| --noPageModel lub-npm | Przełącz, aby nie generować klasy PageModel dla pustego szablonu |

Użyj przełącznika `-h` , aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Aby zapoznać się z `dotnet aspnet-codegenerator razorpage`przykładem, zobacz Tworzenie [szkieletu modelu filmu](/aspnet/core/tutorials/razor-pages/model) .

### Identity

Wyświetlanie [szkieletu Identity ](/aspnet/core/security/authentication/scaffold-identity)
