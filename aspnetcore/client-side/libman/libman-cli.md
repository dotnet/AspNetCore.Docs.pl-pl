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
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="3e96c-103">Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e96c-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="3e96c-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3e96c-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="3e96c-105">[LibMan](xref:client-side/libman/index) CLI to narzędzie międzyplatformowe, które jest obsługiwane wszędzie tam, gdzie obsługiwany jest program .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e96c-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e96c-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3e96c-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="3e96c-107">Instalacja</span><span class="sxs-lookup"><span data-stu-id="3e96c-107">Installation</span></span>

<span data-ttu-id="3e96c-108">Aby zainstalować libman cli:</span><span class="sxs-lookup"><span data-stu-id="3e96c-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="3e96c-109">[Narzędzie globalne .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="3e96c-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="3e96c-110">Aby zainstalować libman cli z określonego źródła pakietu NuGet:</span><span class="sxs-lookup"><span data-stu-id="3e96c-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="3e96c-111">W poprzednim przykładzie narzędzie globalne .NET Core jest instalowane z pliku *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.94-g606058a278.nupkg* komputera.</span><span class="sxs-lookup"><span data-stu-id="3e96c-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="3e96c-112">Sposób użycia</span><span class="sxs-lookup"><span data-stu-id="3e96c-112">Usage</span></span>

<span data-ttu-id="3e96c-113">Po pomyślnej instalacji interfejsu wiersza polecenia można użyć następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="3e96c-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="3e96c-114">Aby wyświetlić zainstalowaną wersję interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="3e96c-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="3e96c-115">Aby wyświetlić dostępne polecenia interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="3e96c-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="3e96c-116">Poprzednie polecenie wyświetla dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="3e96c-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="3e96c-117">W poniższych sekcjach opisano dostępne polecenia interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="3e96c-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="3e96c-118">Inicjowanie libmana w projekcie</span><span class="sxs-lookup"><span data-stu-id="3e96c-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="3e96c-119">Polecenie `libman init` tworzy plik *libman.json,* jeśli taki nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="3e96c-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="3e96c-120">Plik zostanie utworzony przy obliczu domyślnej zawartości szablonu elementu.</span><span class="sxs-lookup"><span data-stu-id="3e96c-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e96c-121">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="3e96c-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3e96c-122">Opcje</span><span class="sxs-lookup"><span data-stu-id="3e96c-122">Options</span></span>

<span data-ttu-id="3e96c-123">Dla `libman init` polecenia dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="3e96c-124">Ścieżka względem bieżącego folderu.</span><span class="sxs-lookup"><span data-stu-id="3e96c-124">A path relative to the current folder.</span></span> <span data-ttu-id="3e96c-125">Pliki biblioteki są instalowane w tej lokalizacji, jeśli nie zdefiniowano żadnej `destination` właściwości dla biblioteki w pliku *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3e96c-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="3e96c-126">Wartość `<PATH>` jest zapisywana `defaultDestination` na własność *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3e96c-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="3e96c-127">Dostawca do użycia, jeśli żaden dostawca nie jest zdefiniowany dla danej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3e96c-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="3e96c-128">Wartość `<PROVIDER>` jest zapisywana `defaultProvider` na własność *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3e96c-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="3e96c-129">Zastąp `<PROVIDER>` jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="3e96c-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e96c-130">Przykłady</span><span class="sxs-lookup"><span data-stu-id="3e96c-130">Examples</span></span>

<span data-ttu-id="3e96c-131">Aby utworzyć plik *libman.json* w projekcie ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3e96c-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="3e96c-132">Przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="3e96c-132">Navigate to the project root.</span></span>
* <span data-ttu-id="3e96c-133">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3e96c-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="3e96c-134">Wpisz nazwę domyślnego dostawcy lub `Enter` naciśnij, aby użyć domyślnego dostawcy usługi CDNJS.</span><span class="sxs-lookup"><span data-stu-id="3e96c-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="3e96c-135">Prawidłowe wartości to:</span><span class="sxs-lookup"><span data-stu-id="3e96c-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init command - domyślny dostawca](_static/libman-init-provider.png)

<span data-ttu-id="3e96c-137">Plik *libman.json* jest dodawany do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="3e96c-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="3e96c-138">Dodawanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="3e96c-138">Add library files</span></span>

<span data-ttu-id="3e96c-139">Polecenie `libman install` pobiera i instaluje pliki biblioteki w projekcie.</span><span class="sxs-lookup"><span data-stu-id="3e96c-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="3e96c-140">Plik *libman.json* jest dodawany, jeśli taki nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="3e96c-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="3e96c-141">Plik *libman.json* jest modyfikowany w celu przechowywania szczegółów konfiguracji plików biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3e96c-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e96c-142">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="3e96c-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e96c-143">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3e96c-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3e96c-144">Nazwa biblioteki do zainstalowania.</span><span class="sxs-lookup"><span data-stu-id="3e96c-144">The name of the library to install.</span></span> <span data-ttu-id="3e96c-145">Nazwa ta może zawierać notację numeru `@1.2.0`wersji (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="3e96c-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3e96c-146">Opcje</span><span class="sxs-lookup"><span data-stu-id="3e96c-146">Options</span></span>

<span data-ttu-id="3e96c-147">Dla `libman install` polecenia dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="3e96c-148">Lokalizacja, aby zainstalować bibliotekę.</span><span class="sxs-lookup"><span data-stu-id="3e96c-148">The location to install the library.</span></span> <span data-ttu-id="3e96c-149">Jeśli nie zostanie określona, używana jest lokalizacja domyślna.</span><span class="sxs-lookup"><span data-stu-id="3e96c-149">If not specified, the default location is used.</span></span> <span data-ttu-id="3e96c-150">Jeśli `defaultDestination` w *libman.json*nie określono właściwości, ta opcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="3e96c-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="3e96c-151">Określ nazwę pliku do zainstalowania z biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3e96c-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="3e96c-152">Jeśli nie zostanie określony, wszystkie pliki z biblioteki są zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="3e96c-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="3e96c-153">Podaj jedną `--files` opcję na plik do zainstalowania.</span><span class="sxs-lookup"><span data-stu-id="3e96c-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="3e96c-154">Ścieżki względne są również obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3e96c-154">Relative paths are supported too.</span></span> <span data-ttu-id="3e96c-155">Na przykład: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="3e96c-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="3e96c-156">Nazwa dostawcy do użycia w przypadku nabycia biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3e96c-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="3e96c-157">Zastąp `<PROVIDER>` jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="3e96c-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="3e96c-158">Jeśli nie zostanie `defaultProvider` określony, używana jest właściwość w *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="3e96c-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="3e96c-159">Jeśli `defaultProvider` w *libman.json*nie określono właściwości, ta opcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="3e96c-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e96c-160">Przykłady</span><span class="sxs-lookup"><span data-stu-id="3e96c-160">Examples</span></span>

<span data-ttu-id="3e96c-161">Należy wziąć pod uwagę następujący plik *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="3e96c-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="3e96c-162">Aby zainstalować plik jQuery w wersji 3.2.1 *jquery.min.js* w folderze *wwwroot/scripts/jquery* przy użyciu dostawcy CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3e96c-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="3e96c-163">Plik *libman.json* przypomina następujące:</span><span class="sxs-lookup"><span data-stu-id="3e96c-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="3e96c-164">Aby zainstalować pliki *calendar.js* i *calendar.css* z \*języka C:\\temp\\contosoCalendar\\ \* przy użyciu dostawcy systemu plików:</span><span class="sxs-lookup"><span data-stu-id="3e96c-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="3e96c-165">Następujący monit pojawia się z dwóch powodów:</span><span class="sxs-lookup"><span data-stu-id="3e96c-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="3e96c-166">Plik *libman.json* nie zawiera `defaultDestination` właściwości.</span><span class="sxs-lookup"><span data-stu-id="3e96c-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="3e96c-167">Polecenie `libman install` nie zawiera `-d|--destination` tej opcji.</span><span class="sxs-lookup"><span data-stu-id="3e96c-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman install command - miejsce docelowe](_static/libman-install-destination.png)

<span data-ttu-id="3e96c-169">Po zaakceptowaniu domyślnego miejsca docelowego plik *libman.json* jest podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="3e96c-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="3e96c-170">Przywracanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="3e96c-170">Restore library files</span></span>

<span data-ttu-id="3e96c-171">Polecenie `libman restore` instaluje pliki biblioteki zdefiniowane w *pliku libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3e96c-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="3e96c-172">Mają zastosowanie następujące zasady:</span><span class="sxs-lookup"><span data-stu-id="3e96c-172">The following rules apply:</span></span>

* <span data-ttu-id="3e96c-173">Jeśli w katalogu głównym projektu nie istnieje żaden plik *libman.json,* zwracany jest błąd.</span><span class="sxs-lookup"><span data-stu-id="3e96c-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="3e96c-174">Jeśli biblioteka określa dostawcę, `defaultProvider` właściwość w *libman.json* jest ignorowana.</span><span class="sxs-lookup"><span data-stu-id="3e96c-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="3e96c-175">Jeśli biblioteka określa miejsce `defaultDestination` docelowe, właściwość w *libman.json* jest ignorowana.</span><span class="sxs-lookup"><span data-stu-id="3e96c-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e96c-176">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="3e96c-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3e96c-177">Opcje</span><span class="sxs-lookup"><span data-stu-id="3e96c-177">Options</span></span>

<span data-ttu-id="3e96c-178">Dla `libman restore` polecenia dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e96c-179">Przykłady</span><span class="sxs-lookup"><span data-stu-id="3e96c-179">Examples</span></span>

<span data-ttu-id="3e96c-180">Aby przywrócić pliki biblioteki zdefiniowane w *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="3e96c-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="3e96c-181">Usuwanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="3e96c-181">Delete library files</span></span>

<span data-ttu-id="3e96c-182">Polecenie `libman clean` usuwa pliki biblioteki wcześniej przywrócone za pośrednictwem programu LibMan.</span><span class="sxs-lookup"><span data-stu-id="3e96c-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="3e96c-183">Foldery, które stają się puste po tej operacji są usuwane.</span><span class="sxs-lookup"><span data-stu-id="3e96c-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="3e96c-184">Konfiguracje skojarzone z plikami `libraries` biblioteki we właściwości *libman.json* nie są usuwane.</span><span class="sxs-lookup"><span data-stu-id="3e96c-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e96c-185">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="3e96c-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3e96c-186">Opcje</span><span class="sxs-lookup"><span data-stu-id="3e96c-186">Options</span></span>

<span data-ttu-id="3e96c-187">Dla `libman clean` polecenia dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e96c-188">Przykłady</span><span class="sxs-lookup"><span data-stu-id="3e96c-188">Examples</span></span>

<span data-ttu-id="3e96c-189">Aby usunąć pliki biblioteki zainstalowane za pośrednictwem programu LibMan:</span><span class="sxs-lookup"><span data-stu-id="3e96c-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="3e96c-190">Odinstalowywanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="3e96c-190">Uninstall library files</span></span>

<span data-ttu-id="3e96c-191">Polecenie `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="3e96c-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="3e96c-192">Usuwa wszystkie pliki skojarzone z określoną biblioteką z miejsca docelowego w *pliku libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3e96c-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="3e96c-193">Usuwa skojarzoną konfigurację biblioteki z *pliku libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3e96c-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="3e96c-194">Błąd występuje, gdy:</span><span class="sxs-lookup"><span data-stu-id="3e96c-194">An error occurs when:</span></span>

* <span data-ttu-id="3e96c-195">W katalogu głównym projektu nie istnieje żaden plik *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="3e96c-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3e96c-196">Określona biblioteka nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="3e96c-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="3e96c-197">Jeśli zainstalowana jest więcej niż jedna biblioteka o tej samej nazwie, zostanie wyświetlony monit o wybranie jej.</span><span class="sxs-lookup"><span data-stu-id="3e96c-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e96c-198">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="3e96c-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e96c-199">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3e96c-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3e96c-200">Nazwa biblioteki do odinstalowania.</span><span class="sxs-lookup"><span data-stu-id="3e96c-200">The name of the library to uninstall.</span></span> <span data-ttu-id="3e96c-201">Nazwa ta może zawierać notację numeru `@1.2.0`wersji (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="3e96c-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3e96c-202">Opcje</span><span class="sxs-lookup"><span data-stu-id="3e96c-202">Options</span></span>

<span data-ttu-id="3e96c-203">Dla `libman uninstall` polecenia dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e96c-204">Przykłady</span><span class="sxs-lookup"><span data-stu-id="3e96c-204">Examples</span></span>

<span data-ttu-id="3e96c-205">Należy wziąć pod uwagę następujący plik *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="3e96c-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="3e96c-206">Aby odinstalować jQuery, którekolwiek z następujących poleceń zakończy się pomyślnie:</span><span class="sxs-lookup"><span data-stu-id="3e96c-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="3e96c-207">Aby odinstalować pliki Lodash zainstalowane za pośrednictwem `filesystem` dostawcy:</span><span class="sxs-lookup"><span data-stu-id="3e96c-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="3e96c-208">Aktualizowanie wersji biblioteki</span><span class="sxs-lookup"><span data-stu-id="3e96c-208">Update library version</span></span>

<span data-ttu-id="3e96c-209">Polecenie `libman update` aktualizuje bibliotekę zainstalowaną za pośrednictwem programu LibMan do określonej wersji.</span><span class="sxs-lookup"><span data-stu-id="3e96c-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="3e96c-210">Błąd występuje, gdy:</span><span class="sxs-lookup"><span data-stu-id="3e96c-210">An error occurs when:</span></span>

* <span data-ttu-id="3e96c-211">W katalogu głównym projektu nie istnieje żaden plik *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="3e96c-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3e96c-212">Określona biblioteka nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="3e96c-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="3e96c-213">Jeśli zainstalowana jest więcej niż jedna biblioteka o tej samej nazwie, zostanie wyświetlony monit o wybranie jej.</span><span class="sxs-lookup"><span data-stu-id="3e96c-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e96c-214">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="3e96c-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e96c-215">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3e96c-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3e96c-216">Nazwa biblioteki do aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="3e96c-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="3e96c-217">Opcje</span><span class="sxs-lookup"><span data-stu-id="3e96c-217">Options</span></span>

<span data-ttu-id="3e96c-218">Dla `libman update` polecenia dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="3e96c-219">Uzyskaj najnowszą wersję biblioteki w wersji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="3e96c-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="3e96c-220">Uzyskaj określoną wersję biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3e96c-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e96c-221">Przykłady</span><span class="sxs-lookup"><span data-stu-id="3e96c-221">Examples</span></span>

* <span data-ttu-id="3e96c-222">Aby zaktualizować jQuery do najnowszej wersji:</span><span class="sxs-lookup"><span data-stu-id="3e96c-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="3e96c-223">Aby zaktualizować jQuery do wersji 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="3e96c-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="3e96c-224">Aby zaktualizować jQuery do najnowszej wersji wstępnej:</span><span class="sxs-lookup"><span data-stu-id="3e96c-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="3e96c-225">Zarządzanie pamięcią podręczną biblioteki</span><span class="sxs-lookup"><span data-stu-id="3e96c-225">Manage library cache</span></span>

<span data-ttu-id="3e96c-226">Polecenie `libman cache` zarządza pamięcią podręczną biblioteki LibMan.</span><span class="sxs-lookup"><span data-stu-id="3e96c-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="3e96c-227">Dostawca `filesystem` nie używa pamięci podręcznej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3e96c-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e96c-228">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="3e96c-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e96c-229">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3e96c-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="3e96c-230">Używany tylko `clean` z poleceniem.</span><span class="sxs-lookup"><span data-stu-id="3e96c-230">Only used with the `clean` command.</span></span> <span data-ttu-id="3e96c-231">Określa pamięć podręczną dostawcy do czyszczenia.</span><span class="sxs-lookup"><span data-stu-id="3e96c-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="3e96c-232">Prawidłowe wartości to:</span><span class="sxs-lookup"><span data-stu-id="3e96c-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="3e96c-233">Opcje</span><span class="sxs-lookup"><span data-stu-id="3e96c-233">Options</span></span>

<span data-ttu-id="3e96c-234">Dla `libman cache` polecenia dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="3e96c-235">Wyświetl listę nazw plików, które są buforowane.</span><span class="sxs-lookup"><span data-stu-id="3e96c-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="3e96c-236">Wyświetl listę nazw bibliotek, które są buforowane.</span><span class="sxs-lookup"><span data-stu-id="3e96c-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e96c-237">Przykłady</span><span class="sxs-lookup"><span data-stu-id="3e96c-237">Examples</span></span>

* <span data-ttu-id="3e96c-238">Aby wyświetlić nazwy buforowanych bibliotek na dostawcę, użyj jednego z następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="3e96c-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="3e96c-239">Wyświetlane są dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="3e96c-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="3e96c-240">Aby wyświetlić nazwy buforowanych plików biblioteki na dostawcę:</span><span class="sxs-lookup"><span data-stu-id="3e96c-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="3e96c-241">Wyświetlane są dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="3e96c-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="3e96c-242">Zwróć uwagę, że poprzednie dane wyjściowe pokazują, że jQuery wersje 3.2.1 i 3.3.1 są buforowane w ramach dostawcy cdnjs.</span><span class="sxs-lookup"><span data-stu-id="3e96c-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="3e96c-243">Aby opróżnić pamięć podręczną biblioteki dla dostawcy usług CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3e96c-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="3e96c-244">Po opróżnieniu pamięci podręcznej dostawcy `libman cache list` usług CDNJS polecenie wyświetla następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="3e96c-245">Aby opróżnić pamięć podręczną dla wszystkich obsługiwanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="3e96c-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="3e96c-246">Po opróżnieniu wszystkich pamięci `libman cache list` podręcznych dostawcy polecenie wyświetla następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="3e96c-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="3e96c-247">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3e96c-247">Additional resources</span></span>

* [<span data-ttu-id="3e96c-248">Instalowanie narzędzia globalnego</span><span class="sxs-lookup"><span data-stu-id="3e96c-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="3e96c-249">Repozytorium LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="3e96c-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
