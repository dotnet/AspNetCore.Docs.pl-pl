---
title: Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core
author: scottaddie
description: Dowiedz się, jak używać interfejsu wiersza polecenia LibMan w projekcie ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664634"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI to narzędzie międzyplatformowe, które jest obsługiwane wszędzie tam, gdzie obsługiwany jest program .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalacja

Aby zainstalować libman cli:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[Narzędzie globalne .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.

Aby zainstalować libman cli z określonego źródła pakietu NuGet:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

W poprzednim przykładzie narzędzie globalne .NET Core jest instalowane z pliku *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.94-g606058a278.nupkg* komputera.

## <a name="usage"></a>Sposób użycia

Po pomyślnej instalacji interfejsu wiersza polecenia można użyć następującego polecenia:

```console
libman
```

Aby wyświetlić zainstalowaną wersję interfejsu wiersza polecenia:

```console
libman --version
```

Aby wyświetlić dostępne polecenia interfejsu wiersza polecenia:

```console
libman --help
```

Poprzednie polecenie wyświetla dane wyjściowe podobne do następujących:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

W poniższych sekcjach opisano dostępne polecenia interfejsu wiersza polecenia.

## <a name="initialize-libman-in-the-project"></a>Inicjowanie libmana w projekcie

Polecenie `libman init` tworzy plik *libman.json,* jeśli taki nie istnieje. Plik zostanie utworzony przy obliczu domyślnej zawartości szablonu elementu.

### <a name="synopsis"></a>Streszczenie

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opcje

Dla `libman init` polecenia dostępne są następujące opcje:

* `-d|--default-destination <PATH>`

  Ścieżka względem bieżącego folderu. Pliki biblioteki są instalowane w tej lokalizacji, jeśli nie zdefiniowano żadnej `destination` właściwości dla biblioteki w pliku *libman.json*. Wartość `<PATH>` jest zapisywana `defaultDestination` na własność *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Dostawca do użycia, jeśli żaden dostawca nie jest zdefiniowany dla danej biblioteki. Wartość `<PROVIDER>` jest zapisywana `defaultProvider` na własność *libman.json*. Zastąp `<PROVIDER>` jedną z następujących wartości:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby utworzyć plik *libman.json* w projekcie ASP.NET Core:

* Przejdź do katalogu głównego projektu.
* Uruchom następujące polecenie:

  ```console
  libman init
  ```

* Wpisz nazwę domyślnego dostawcy lub `Enter` naciśnij, aby użyć domyślnego dostawcy usługi CDNJS. Prawidłowe wartości to:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init command - domyślny dostawca](_static/libman-init-provider.png)

Plik *libman.json* jest dodawany do katalogu głównego projektu z następującą zawartością:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Dodawanie plików biblioteki

Polecenie `libman install` pobiera i instaluje pliki biblioteki w projekcie. Plik *libman.json* jest dodawany, jeśli taki nie istnieje. Plik *libman.json* jest modyfikowany w celu przechowywania szczegółów konfiguracji plików biblioteki.

### <a name="synopsis"></a>Streszczenie

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do zainstalowania. Nazwa ta może zawierać notację numeru `@1.2.0`wersji (na przykład ).

### <a name="options"></a>Opcje

Dla `libman install` polecenia dostępne są następujące opcje:

* `-d|--destination <PATH>`

  Lokalizacja, aby zainstalować bibliotekę. Jeśli nie zostanie określona, używana jest lokalizacja domyślna. Jeśli `defaultDestination` w *libman.json*nie określono właściwości, ta opcja jest wymagana.

* `--files <FILE>`

  Określ nazwę pliku do zainstalowania z biblioteki. Jeśli nie zostanie określony, wszystkie pliki z biblioteki są zainstalowane. Podaj jedną `--files` opcję na plik do zainstalowania. Ścieżki względne są również obsługiwane. Na przykład: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Nazwa dostawcy do użycia w przypadku nabycia biblioteki. Zastąp `<PROVIDER>` jedną z następujących wartości:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Jeśli nie zostanie `defaultProvider` określony, używana jest właściwość w *libman.json.* Jeśli `defaultProvider` w *libman.json*nie określono właściwości, ta opcja jest wymagana.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Należy wziąć pod uwagę następujący plik *libman.json:*

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Aby zainstalować plik jQuery w wersji 3.2.1 *jquery.min.js* w folderze *wwwroot/scripts/jquery* przy użyciu dostawcy CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Plik *libman.json* przypomina następujące:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Aby zainstalować pliki *calendar.js* i *calendar.css* z *języka C:\\temp\\contosoCalendar\\ * przy użyciu dostawcy systemu plików:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Następujący monit pojawia się z dwóch powodów:

* Plik *libman.json* nie zawiera `defaultDestination` właściwości.
* Polecenie `libman install` nie zawiera `-d|--destination` tej opcji.

![libman install command - miejsce docelowe](_static/libman-install-destination.png)

Po zaakceptowaniu domyślnego miejsca docelowego plik *libman.json* jest podobny do następującego:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Przywracanie plików biblioteki

Polecenie `libman restore` instaluje pliki biblioteki zdefiniowane w *pliku libman.json*. Mają zastosowanie następujące zasady:

* Jeśli w katalogu głównym projektu nie istnieje żaden plik *libman.json,* zwracany jest błąd.
* Jeśli biblioteka określa dostawcę, `defaultProvider` właściwość w *libman.json* jest ignorowana.
* Jeśli biblioteka określa miejsce `defaultDestination` docelowe, właściwość w *libman.json* jest ignorowana.

### <a name="synopsis"></a>Streszczenie

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opcje

Dla `libman restore` polecenia dostępne są następujące opcje:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby przywrócić pliki biblioteki zdefiniowane w *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Usuwanie plików biblioteki

Polecenie `libman clean` usuwa pliki biblioteki wcześniej przywrócone za pośrednictwem programu LibMan. Foldery, które stają się puste po tej operacji są usuwane. Konfiguracje skojarzone z plikami `libraries` biblioteki we właściwości *libman.json* nie są usuwane.

### <a name="synopsis"></a>Streszczenie

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opcje

Dla `libman clean` polecenia dostępne są następujące opcje:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby usunąć pliki biblioteki zainstalowane za pośrednictwem programu LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Odinstalowywanie plików biblioteki

Polecenie `libman uninstall`:

* Usuwa wszystkie pliki skojarzone z określoną biblioteką z miejsca docelowego w *pliku libman.json*.
* Usuwa skojarzoną konfigurację biblioteki z *pliku libman.json*.

Błąd występuje, gdy:

* W katalogu głównym projektu nie istnieje żaden plik *libman.json.*
* Określona biblioteka nie istnieje.

Jeśli zainstalowana jest więcej niż jedna biblioteka o tej samej nazwie, zostanie wyświetlony monit o wybranie jej.

### <a name="synopsis"></a>Streszczenie

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do odinstalowania. Nazwa ta może zawierać notację numeru `@1.2.0`wersji (na przykład ).

### <a name="options"></a>Opcje

Dla `libman uninstall` polecenia dostępne są następujące opcje:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Należy wziąć pod uwagę następujący plik *libman.json:*

[!code-json[](samples/LibManSample/libman.json)]

* Aby odinstalować jQuery, którekolwiek z następujących poleceń zakończy się pomyślnie:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Aby odinstalować pliki Lodash zainstalowane za pośrednictwem `filesystem` dostawcy:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Aktualizowanie wersji biblioteki

Polecenie `libman update` aktualizuje bibliotekę zainstalowaną za pośrednictwem programu LibMan do określonej wersji.

Błąd występuje, gdy:

* W katalogu głównym projektu nie istnieje żaden plik *libman.json.*
* Określona biblioteka nie istnieje.

Jeśli zainstalowana jest więcej niż jedna biblioteka o tej samej nazwie, zostanie wyświetlony monit o wybranie jej.

### <a name="synopsis"></a>Streszczenie

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do aktualizacji.

### <a name="options"></a>Opcje

Dla `libman update` polecenia dostępne są następujące opcje:

* `-pre`

  Uzyskaj najnowszą wersję biblioteki w wersji wstępnej.

* `--to <VERSION>`

  Uzyskaj określoną wersję biblioteki.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

* Aby zaktualizować jQuery do najnowszej wersji:

  ```console
  libman update jquery
  ```

* Aby zaktualizować jQuery do wersji 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Aby zaktualizować jQuery do najnowszej wersji wstępnej:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Zarządzanie pamięcią podręczną biblioteki

Polecenie `libman cache` zarządza pamięcią podręczną biblioteki LibMan. Dostawca `filesystem` nie używa pamięci podręcznej biblioteki.

### <a name="synopsis"></a>Streszczenie

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumenty

`PROVIDER`

Używany tylko `clean` z poleceniem. Określa pamięć podręczną dostawcy do czyszczenia. Prawidłowe wartości to:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opcje

Dla `libman cache` polecenia dostępne są następujące opcje:

* `--files`

  Wyświetl listę nazw plików, które są buforowane.

* `--libraries`

  Wyświetl listę nazw bibliotek, które są buforowane.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

* Aby wyświetlić nazwy buforowanych bibliotek na dostawcę, użyj jednego z następujących poleceń:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Wyświetlane są dane wyjściowe podobne do następujących:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Aby wyświetlić nazwy buforowanych plików biblioteki na dostawcę:

  ```console
  libman cache list --files
  ```

  Wyświetlane są dane wyjściowe podobne do następujących:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Zwróć uwagę, że poprzednie dane wyjściowe pokazują, że jQuery wersje 3.2.1 i 3.3.1 są buforowane w ramach dostawcy cdnjs.

* Aby opróżnić pamięć podręczną biblioteki dla dostawcy usług CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Po opróżnieniu pamięci podręcznej dostawcy `libman cache list` usług CDNJS polecenie wyświetla następujące informacje:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Aby opróżnić pamięć podręczną dla wszystkich obsługiwanych dostawców:

  ```console
  libman cache clean
  ```

  Po opróżnieniu wszystkich pamięci `libman cache list` podręcznych dostawcy polecenie wyświetla następujące informacje:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Instalowanie narzędzia globalnego](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Repozytorium LibMan GitHub](https://github.com/aspnet/LibraryManager)
