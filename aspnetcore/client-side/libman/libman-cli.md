---
title: Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core
author: scottaddie
description: Dowiedz się, jak używać interfejsu wiersza polecenia LibMan w projekcie ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054752"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Interfejs wiersza polecenia [LibMan](xref:client-side/libman/index) to międzyplatformowe narzędzie, które jest obsługiwane wszędzie tam, gdzie jest obsługiwane środowisko .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalacja

Aby zainstalować interfejs wiersza polecenia LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .

Aby zainstalować interfejs wiersza polecenia LibMan z określonego źródła pakietu NuGet:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

W poprzednim przykładzie jest instalowane narzędzie globalne .NET Core z pliku *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* lokalnego komputera z systemem Windows.

## <a name="usage"></a>Użycie

Po pomyślnej instalacji interfejsu wiersza polecenia może być używane następujące polecenie:

```console
libman
```

Aby wyświetlić zainstalowaną wersję interfejsu wiersza polecenia:

```console
libman --version
```

Wyświetlanie dostępnych poleceń interfejsu wiersza polecenia:

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

W poniższych sekcjach znajduje się opis dostępnych poleceń interfejsu wiersza polecenia.

## <a name="initialize-libman-in-the-project"></a>Inicjuj LibMan w projekcie

`libman init`Polecenie tworzy *libman.jsw* pliku, jeśli taki nie istnieje. Plik jest tworzony z domyślną zawartością szablonu elementu.

### <a name="synopsis"></a>Streszczenie

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `libman init` polecenia:

* `-d|--default-destination <PATH>`

  Ścieżka względna do bieżącego folderu. Pliki bibliotek są instalowane w tej lokalizacji, jeśli nie `destination` zdefiniowano żadnej właściwości dla biblioteki w *libman.js*. `<PATH>`Wartość jest zapisywana we `defaultDestination` właściwości *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Dostawca, który ma być używany, jeśli nie zdefiniowano żadnego dostawcy dla danej biblioteki. `<PROVIDER>`Wartość jest zapisywana we `defaultProvider` właściwości *libman.json*. Zamień `<PROVIDER>` na jedną z następujących wartości:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby utworzyć *libman.jsw* pliku w ASP.NET Core projekcie:

* Przejdź do katalogu głównego projektu.
* Uruchom następujące polecenie:

  ```console
  libman init
  ```

* Wpisz nazwę domyślnego dostawcy lub naciśnij przycisk, `Enter` Aby użyć domyślnego dostawcy CDNJS. Prawidłowe wartości to:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman init — polecenie — domyślny dostawca](_static/libman-init-provider.png)

*libman.jsw* pliku zostanie dodany do katalogu głównego projektu z następującą zawartością:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Dodaj pliki biblioteki

`libman install`Polecenie pobiera i instaluje pliki bibliotek w projekcie. Jeśli jeden nie istnieje, zostanie dodany *libman.js* pliku. *libman.jsw* pliku został zmodyfikowany w celu przechowywania szczegółów konfiguracji dla plików biblioteki.

### <a name="synopsis"></a>Streszczenie

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do zainstalowania. Ta nazwa może zawierać notację numeru wersji (na przykład `@1.2.0` ).

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `libman install` polecenia:

* `-d|--destination <PATH>`

  Lokalizacja, w której ma zostać zainstalowana Biblioteka. Jeśli nie zostanie określony, zostanie użyta domyślna lokalizacja. Jeśli żadna `defaultDestination` Właściwość nie jest określona w *libman.jsna*, ta opcja jest wymagana.

* `--files <FILE>`

  Określ nazwę pliku, który ma zostać zainstalowany z biblioteki. Jeśli nie zostanie określony, wszystkie pliki z biblioteki są zainstalowane. Podaj jedną `--files` opcję dla każdego pliku, który ma zostać zainstalowany. Ścieżki względne są również obsługiwane. Na przykład: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Nazwa dostawcy do użycia podczas pozyskiwania biblioteki. Zamień `<PROVIDER>` na jedną z następujących wartości:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Jeśli nie zostanie określony, `defaultProvider` Właściwość w *libman.js* jest używana. Jeśli żadna `defaultProvider` Właściwość nie jest określona w *libman.jsna*, ta opcja jest wymagana.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Rozważmy następujące *libman.js* pliku:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Aby zainstalować plik jQuery w wersji 3.2.1 *jquery.min.js* do folderu *wwwroot/scripts/jQuery* przy użyciu dostawcy CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

*libman.jsw* pliku jest podobny do następującego:

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

Aby zainstalować pliki *calendar.js* i *Calendar. css* z *dysku C: \\ temp \\ contosoCalendar \\* przy użyciu dostawcy systemu plików:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Następujący monit pojawia się z dwóch powodów:

* *libman.jsw* pliku nie zawiera `defaultDestination` właściwości.
* `libman install`Polecenie nie zawiera `-d|--destination` opcji.

![Libman — polecenie instalacji — miejsce docelowe](_static/libman-install-destination.png)

Po zaakceptowaniu domyślnego miejsca docelowego *libman.jsw* pliku przypomina następujące:

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

## <a name="restore-library-files"></a>Przywróć pliki biblioteki

`libman restore`Polecenie instaluje pliki bibliotek zdefiniowane w *libman.jsna*. Mają zastosowanie następujące zasady:

* Jeśli w katalogu głównym projektu nie istnieje *libman.jsw* pliku, zwracany jest błąd.
* Jeśli Biblioteka określa dostawcę, `defaultProvider` Właściwość w *libman.js* jest ignorowana.
* Jeśli Biblioteka określa miejsce docelowe, `defaultDestination` Właściwość w *libman.js* jest ignorowana.

### <a name="synopsis"></a>Streszczenie

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `libman restore` polecenia:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby przywrócić pliki biblioteki zdefiniowane w *libman.jsna*:

```console
libman restore
```

## <a name="delete-library-files"></a>Usuń pliki biblioteki

`libman clean`Polecenie usuwa pliki biblioteki, które zostały wcześniej przywrócone za pośrednictwem LibMan. Foldery, które staną się puste po usunięciu tej operacji. Pliki biblioteki "skojarzone konfiguracje we `libraries` właściwości *libman.json* nie są usuwane.

### <a name="synopsis"></a>Streszczenie

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `libman clean` polecenia:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Aby usunąć pliki biblioteki zainstalowane za pośrednictwem LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Odinstaluj pliki biblioteki

Polecenie `libman uninstall`:

* Usuwa wszystkie pliki skojarzone z określoną biblioteką z lokalizacji docelowej w *libman.jsna*.
* Usuwa skojarzoną konfigurację biblioteki z *libman.jsna*.

Wystąpił błąd, gdy:

* Brak *libman.js* w pliku w katalogu głównym projektu.
* Określona biblioteka nie istnieje.

Jeśli zainstalowano więcej niż jedną bibliotekę o tej samej nazwie, zostanie wyświetlony monit o wybranie jednej z nich.

### <a name="synopsis"></a>Streszczenie

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do odinstalowania. Ta nazwa może zawierać notację numeru wersji (na przykład `@1.2.0` ).

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `libman uninstall` polecenia:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Przykłady

Rozważmy następujące *libman.js* pliku:

[!code-json[](samples/LibManSample/libman.json)]

* Aby odinstalować jQuery, jedno z następujących poleceń powiedzie się:

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

## <a name="update-library-version"></a>Zaktualizuj wersję biblioteki

`libman update`Polecenie aktualizuje bibliotekę zainstalowaną za pośrednictwem LibMan do określonej wersji.

Wystąpił błąd, gdy:

* Brak *libman.js* w pliku w katalogu głównym projektu.
* Określona biblioteka nie istnieje.

Jeśli zainstalowano więcej niż jedną bibliotekę o tej samej nazwie, zostanie wyświetlony monit o wybranie jednej z nich.

### <a name="synopsis"></a>Streszczenie

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Nazwa biblioteki do zaktualizowania.

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `libman update` polecenia:

* `-pre`

  Uzyskaj najnowszą wersję wstępną biblioteki.

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

## <a name="manage-library-cache"></a>Zarządzaj pamięcią podręczną biblioteki

`libman cache`Polecenie zarządza pamięcią podręczną biblioteki LibMan. `filesystem`Dostawca nie korzysta z pamięci podręcznej biblioteki.

### <a name="synopsis"></a>Streszczenie

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumenty

`PROVIDER`

Używane tylko z `clean` poleceniem. Określa pamięć podręczną dostawcy do oczyszczenia. Prawidłowe wartości to:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `libman cache` polecenia:

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

* Aby wyświetlić nazwy plików bibliotek w pamięci podręcznej na dostawcę:

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

  Zwróć uwagę, że powyższe dane wyjściowe pokazują, że jQuery w wersji 3.2.1 i 3.3.1 są buforowane w ramach dostawcy CDNJS.

* Aby opróżnić pamięć podręczną biblioteki dla dostawcy CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Po opróżnieniu pamięci podręcznej dostawcy CDNJS `libman cache list` polecenie wyświetla następujące elementy:

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

  Po opróżnieniu wszystkich pamięci podręcznych dostawcy `libman cache list` polecenie wyświetla następujące elementy:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Instalowanie narzędzia globalnego](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Repozytorium GitHub LibMan](https://github.com/aspnet/LibraryManager)
