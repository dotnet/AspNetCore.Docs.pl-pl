---
title: Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core
author: scottaddie
description: Dowiedz się, jak używać interfejsu wiersza polecenia LibMan w projekcie ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054752"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="9882f-103">Korzystanie z interfejsu wiersza polecenia LibMan z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9882f-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="9882f-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9882f-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9882f-105">Interfejs wiersza polecenia [LibMan](xref:client-side/libman/index) to międzyplatformowe narzędzie, które jest obsługiwane wszędzie tam, gdzie jest obsługiwane środowisko .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9882f-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9882f-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9882f-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="9882f-107">Instalacja</span><span class="sxs-lookup"><span data-stu-id="9882f-107">Installation</span></span>

<span data-ttu-id="9882f-108">Aby zainstalować interfejs wiersza polecenia LibMan:</span><span class="sxs-lookup"><span data-stu-id="9882f-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="9882f-109">[Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .</span><span class="sxs-lookup"><span data-stu-id="9882f-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="9882f-110">Aby zainstalować interfejs wiersza polecenia LibMan z określonego źródła pakietu NuGet:</span><span class="sxs-lookup"><span data-stu-id="9882f-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="9882f-111">W poprzednim przykładzie jest instalowane narzędzie globalne .NET Core z pliku *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* lokalnego komputera z systemem Windows.</span><span class="sxs-lookup"><span data-stu-id="9882f-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="9882f-112">Użycie</span><span class="sxs-lookup"><span data-stu-id="9882f-112">Usage</span></span>

<span data-ttu-id="9882f-113">Po pomyślnej instalacji interfejsu wiersza polecenia może być używane następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9882f-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="9882f-114">Aby wyświetlić zainstalowaną wersję interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="9882f-115">Wyświetlanie dostępnych poleceń interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="9882f-116">Poprzednie polecenie wyświetla dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="9882f-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="9882f-117">W poniższych sekcjach znajduje się opis dostępnych poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9882f-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="9882f-118">Inicjuj LibMan w projekcie</span><span class="sxs-lookup"><span data-stu-id="9882f-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="9882f-119">`libman init`Polecenie tworzy *libman.jsw* pliku, jeśli taki nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="9882f-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="9882f-120">Plik jest tworzony z domyślną zawartością szablonu elementu.</span><span class="sxs-lookup"><span data-stu-id="9882f-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="9882f-121">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9882f-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="9882f-122">Opcje</span><span class="sxs-lookup"><span data-stu-id="9882f-122">Options</span></span>

<span data-ttu-id="9882f-123">Następujące opcje są dostępne dla `libman init` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="9882f-124">Ścieżka względna do bieżącego folderu.</span><span class="sxs-lookup"><span data-stu-id="9882f-124">A path relative to the current folder.</span></span> <span data-ttu-id="9882f-125">Pliki bibliotek są instalowane w tej lokalizacji, jeśli nie `destination` zdefiniowano żadnej właściwości dla biblioteki w *libman.js* .</span><span class="sxs-lookup"><span data-stu-id="9882f-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json* .</span></span> <span data-ttu-id="9882f-126">`<PATH>`Wartość jest zapisywana we `defaultDestination` właściwości *libman.json* .</span><span class="sxs-lookup"><span data-stu-id="9882f-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json* .</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="9882f-127">Dostawca, który ma być używany, jeśli nie zdefiniowano żadnego dostawcy dla danej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="9882f-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="9882f-128">`<PROVIDER>`Wartość jest zapisywana we `defaultProvider` właściwości *libman.json* .</span><span class="sxs-lookup"><span data-stu-id="9882f-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json* .</span></span> <span data-ttu-id="9882f-129">Zamień `<PROVIDER>` na jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="9882f-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="9882f-130">Przykłady</span><span class="sxs-lookup"><span data-stu-id="9882f-130">Examples</span></span>

<span data-ttu-id="9882f-131">Aby utworzyć *libman.jsw* pliku w ASP.NET Core projekcie:</span><span class="sxs-lookup"><span data-stu-id="9882f-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="9882f-132">Przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9882f-132">Navigate to the project root.</span></span>
* <span data-ttu-id="9882f-133">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9882f-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="9882f-134">Wpisz nazwę domyślnego dostawcy lub naciśnij przycisk, `Enter` Aby użyć domyślnego dostawcy CDNJS.</span><span class="sxs-lookup"><span data-stu-id="9882f-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="9882f-135">Prawidłowe wartości to:</span><span class="sxs-lookup"><span data-stu-id="9882f-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman init — polecenie — domyślny dostawca](_static/libman-init-provider.png)

<span data-ttu-id="9882f-137">*libman.jsw* pliku zostanie dodany do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="9882f-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="9882f-138">Dodaj pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="9882f-138">Add library files</span></span>

<span data-ttu-id="9882f-139">`libman install`Polecenie pobiera i instaluje pliki bibliotek w projekcie.</span><span class="sxs-lookup"><span data-stu-id="9882f-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="9882f-140">Jeśli jeden nie istnieje, zostanie dodany *libman.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="9882f-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="9882f-141">*libman.jsw* pliku został zmodyfikowany w celu przechowywania szczegółów konfiguracji dla plików biblioteki.</span><span class="sxs-lookup"><span data-stu-id="9882f-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="9882f-142">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9882f-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="9882f-143">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9882f-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="9882f-144">Nazwa biblioteki do zainstalowania.</span><span class="sxs-lookup"><span data-stu-id="9882f-144">The name of the library to install.</span></span> <span data-ttu-id="9882f-145">Ta nazwa może zawierać notację numeru wersji (na przykład `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="9882f-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="9882f-146">Opcje</span><span class="sxs-lookup"><span data-stu-id="9882f-146">Options</span></span>

<span data-ttu-id="9882f-147">Następujące opcje są dostępne dla `libman install` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="9882f-148">Lokalizacja, w której ma zostać zainstalowana Biblioteka.</span><span class="sxs-lookup"><span data-stu-id="9882f-148">The location to install the library.</span></span> <span data-ttu-id="9882f-149">Jeśli nie zostanie określony, zostanie użyta domyślna lokalizacja.</span><span class="sxs-lookup"><span data-stu-id="9882f-149">If not specified, the default location is used.</span></span> <span data-ttu-id="9882f-150">Jeśli żadna `defaultDestination` Właściwość nie jest określona w *libman.jsna* , ta opcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="9882f-150">If no `defaultDestination` property is specified in *libman.json* , this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="9882f-151">Określ nazwę pliku, który ma zostać zainstalowany z biblioteki.</span><span class="sxs-lookup"><span data-stu-id="9882f-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="9882f-152">Jeśli nie zostanie określony, wszystkie pliki z biblioteki są zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="9882f-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="9882f-153">Podaj jedną `--files` opcję dla każdego pliku, który ma zostać zainstalowany.</span><span class="sxs-lookup"><span data-stu-id="9882f-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="9882f-154">Ścieżki względne są również obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9882f-154">Relative paths are supported too.</span></span> <span data-ttu-id="9882f-155">Na przykład: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="9882f-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="9882f-156">Nazwa dostawcy do użycia podczas pozyskiwania biblioteki.</span><span class="sxs-lookup"><span data-stu-id="9882f-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="9882f-157">Zamień `<PROVIDER>` na jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="9882f-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="9882f-158">Jeśli nie zostanie określony, `defaultProvider` Właściwość w *libman.js* jest używana.</span><span class="sxs-lookup"><span data-stu-id="9882f-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="9882f-159">Jeśli żadna `defaultProvider` Właściwość nie jest określona w *libman.jsna* , ta opcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="9882f-159">If no `defaultProvider` property is specified in *libman.json* , this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="9882f-160">Przykłady</span><span class="sxs-lookup"><span data-stu-id="9882f-160">Examples</span></span>

<span data-ttu-id="9882f-161">Rozważmy następujące *libman.js* pliku:</span><span class="sxs-lookup"><span data-stu-id="9882f-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="9882f-162">Aby zainstalować plik jQuery w wersji 3.2.1 *jquery.min.js* do folderu *wwwroot/scripts/jQuery* przy użyciu dostawcy CDNJS:</span><span class="sxs-lookup"><span data-stu-id="9882f-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="9882f-163">*libman.jsw* pliku jest podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="9882f-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="9882f-164">Aby zainstalować pliki *calendar.js* i *Calendar. css* z *dysku C: \\ temp \\ contosoCalendar \\* przy użyciu dostawcy systemu plików:</span><span class="sxs-lookup"><span data-stu-id="9882f-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="9882f-165">Następujący monit pojawia się z dwóch powodów:</span><span class="sxs-lookup"><span data-stu-id="9882f-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="9882f-166">*libman.jsw* pliku nie zawiera `defaultDestination` właściwości.</span><span class="sxs-lookup"><span data-stu-id="9882f-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="9882f-167">`libman install`Polecenie nie zawiera `-d|--destination` opcji.</span><span class="sxs-lookup"><span data-stu-id="9882f-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![Libman — polecenie instalacji — miejsce docelowe](_static/libman-install-destination.png)

<span data-ttu-id="9882f-169">Po zaakceptowaniu domyślnego miejsca docelowego *libman.jsw* pliku przypomina następujące:</span><span class="sxs-lookup"><span data-stu-id="9882f-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="9882f-170">Przywróć pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="9882f-170">Restore library files</span></span>

<span data-ttu-id="9882f-171">`libman restore`Polecenie instaluje pliki bibliotek zdefiniowane w *libman.jsna* .</span><span class="sxs-lookup"><span data-stu-id="9882f-171">The `libman restore` command installs library files defined in *libman.json* .</span></span> <span data-ttu-id="9882f-172">Mają zastosowanie następujące zasady:</span><span class="sxs-lookup"><span data-stu-id="9882f-172">The following rules apply:</span></span>

* <span data-ttu-id="9882f-173">Jeśli w katalogu głównym projektu nie istnieje *libman.jsw* pliku, zwracany jest błąd.</span><span class="sxs-lookup"><span data-stu-id="9882f-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="9882f-174">Jeśli Biblioteka określa dostawcę, `defaultProvider` Właściwość w *libman.js* jest ignorowana.</span><span class="sxs-lookup"><span data-stu-id="9882f-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="9882f-175">Jeśli Biblioteka określa miejsce docelowe, `defaultDestination` Właściwość w *libman.js* jest ignorowana.</span><span class="sxs-lookup"><span data-stu-id="9882f-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="9882f-176">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9882f-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="9882f-177">Opcje</span><span class="sxs-lookup"><span data-stu-id="9882f-177">Options</span></span>

<span data-ttu-id="9882f-178">Następujące opcje są dostępne dla `libman restore` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="9882f-179">Przykłady</span><span class="sxs-lookup"><span data-stu-id="9882f-179">Examples</span></span>

<span data-ttu-id="9882f-180">Aby przywrócić pliki biblioteki zdefiniowane w *libman.jsna* :</span><span class="sxs-lookup"><span data-stu-id="9882f-180">To restore the library files defined in *libman.json* :</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="9882f-181">Usuń pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="9882f-181">Delete library files</span></span>

<span data-ttu-id="9882f-182">`libman clean`Polecenie usuwa pliki biblioteki, które zostały wcześniej przywrócone za pośrednictwem LibMan.</span><span class="sxs-lookup"><span data-stu-id="9882f-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="9882f-183">Foldery, które staną się puste po usunięciu tej operacji.</span><span class="sxs-lookup"><span data-stu-id="9882f-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="9882f-184">Pliki biblioteki "skojarzone konfiguracje we `libraries` właściwości *libman.json* nie są usuwane.</span><span class="sxs-lookup"><span data-stu-id="9882f-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="9882f-185">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9882f-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="9882f-186">Opcje</span><span class="sxs-lookup"><span data-stu-id="9882f-186">Options</span></span>

<span data-ttu-id="9882f-187">Następujące opcje są dostępne dla `libman clean` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="9882f-188">Przykłady</span><span class="sxs-lookup"><span data-stu-id="9882f-188">Examples</span></span>

<span data-ttu-id="9882f-189">Aby usunąć pliki biblioteki zainstalowane za pośrednictwem LibMan:</span><span class="sxs-lookup"><span data-stu-id="9882f-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="9882f-190">Odinstaluj pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="9882f-190">Uninstall library files</span></span>

<span data-ttu-id="9882f-191">Polecenie `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="9882f-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="9882f-192">Usuwa wszystkie pliki skojarzone z określoną biblioteką z lokalizacji docelowej w *libman.jsna* .</span><span class="sxs-lookup"><span data-stu-id="9882f-192">Deletes all files associated with the specified library from the destination in *libman.json* .</span></span>
* <span data-ttu-id="9882f-193">Usuwa skojarzoną konfigurację biblioteki z *libman.jsna* .</span><span class="sxs-lookup"><span data-stu-id="9882f-193">Removes the associated library configuration from *libman.json* .</span></span>

<span data-ttu-id="9882f-194">Wystąpił błąd, gdy:</span><span class="sxs-lookup"><span data-stu-id="9882f-194">An error occurs when:</span></span>

* <span data-ttu-id="9882f-195">Brak *libman.js* w pliku w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="9882f-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="9882f-196">Określona biblioteka nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="9882f-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="9882f-197">Jeśli zainstalowano więcej niż jedną bibliotekę o tej samej nazwie, zostanie wyświetlony monit o wybranie jednej z nich.</span><span class="sxs-lookup"><span data-stu-id="9882f-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="9882f-198">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9882f-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="9882f-199">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9882f-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="9882f-200">Nazwa biblioteki do odinstalowania.</span><span class="sxs-lookup"><span data-stu-id="9882f-200">The name of the library to uninstall.</span></span> <span data-ttu-id="9882f-201">Ta nazwa może zawierać notację numeru wersji (na przykład `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="9882f-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="9882f-202">Opcje</span><span class="sxs-lookup"><span data-stu-id="9882f-202">Options</span></span>

<span data-ttu-id="9882f-203">Następujące opcje są dostępne dla `libman uninstall` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="9882f-204">Przykłady</span><span class="sxs-lookup"><span data-stu-id="9882f-204">Examples</span></span>

<span data-ttu-id="9882f-205">Rozważmy następujące *libman.js* pliku:</span><span class="sxs-lookup"><span data-stu-id="9882f-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="9882f-206">Aby odinstalować jQuery, jedno z następujących poleceń powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="9882f-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="9882f-207">Aby odinstalować pliki Lodash zainstalowane za pośrednictwem `filesystem` dostawcy:</span><span class="sxs-lookup"><span data-stu-id="9882f-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="9882f-208">Zaktualizuj wersję biblioteki</span><span class="sxs-lookup"><span data-stu-id="9882f-208">Update library version</span></span>

<span data-ttu-id="9882f-209">`libman update`Polecenie aktualizuje bibliotekę zainstalowaną za pośrednictwem LibMan do określonej wersji.</span><span class="sxs-lookup"><span data-stu-id="9882f-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="9882f-210">Wystąpił błąd, gdy:</span><span class="sxs-lookup"><span data-stu-id="9882f-210">An error occurs when:</span></span>

* <span data-ttu-id="9882f-211">Brak *libman.js* w pliku w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="9882f-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="9882f-212">Określona biblioteka nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="9882f-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="9882f-213">Jeśli zainstalowano więcej niż jedną bibliotekę o tej samej nazwie, zostanie wyświetlony monit o wybranie jednej z nich.</span><span class="sxs-lookup"><span data-stu-id="9882f-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="9882f-214">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9882f-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="9882f-215">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9882f-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="9882f-216">Nazwa biblioteki do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="9882f-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="9882f-217">Opcje</span><span class="sxs-lookup"><span data-stu-id="9882f-217">Options</span></span>

<span data-ttu-id="9882f-218">Następujące opcje są dostępne dla `libman update` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="9882f-219">Uzyskaj najnowszą wersję wstępną biblioteki.</span><span class="sxs-lookup"><span data-stu-id="9882f-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="9882f-220">Uzyskaj określoną wersję biblioteki.</span><span class="sxs-lookup"><span data-stu-id="9882f-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="9882f-221">Przykłady</span><span class="sxs-lookup"><span data-stu-id="9882f-221">Examples</span></span>

* <span data-ttu-id="9882f-222">Aby zaktualizować jQuery do najnowszej wersji:</span><span class="sxs-lookup"><span data-stu-id="9882f-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="9882f-223">Aby zaktualizować jQuery do wersji 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="9882f-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="9882f-224">Aby zaktualizować jQuery do najnowszej wersji wstępnej:</span><span class="sxs-lookup"><span data-stu-id="9882f-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="9882f-225">Zarządzaj pamięcią podręczną biblioteki</span><span class="sxs-lookup"><span data-stu-id="9882f-225">Manage library cache</span></span>

<span data-ttu-id="9882f-226">`libman cache`Polecenie zarządza pamięcią podręczną biblioteki LibMan.</span><span class="sxs-lookup"><span data-stu-id="9882f-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="9882f-227">`filesystem`Dostawca nie korzysta z pamięci podręcznej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="9882f-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="9882f-228">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="9882f-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="9882f-229">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9882f-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="9882f-230">Używane tylko z `clean` poleceniem.</span><span class="sxs-lookup"><span data-stu-id="9882f-230">Only used with the `clean` command.</span></span> <span data-ttu-id="9882f-231">Określa pamięć podręczną dostawcy do oczyszczenia.</span><span class="sxs-lookup"><span data-stu-id="9882f-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="9882f-232">Prawidłowe wartości to:</span><span class="sxs-lookup"><span data-stu-id="9882f-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="9882f-233">Opcje</span><span class="sxs-lookup"><span data-stu-id="9882f-233">Options</span></span>

<span data-ttu-id="9882f-234">Następujące opcje są dostępne dla `libman cache` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9882f-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="9882f-235">Wyświetl listę nazw plików, które są buforowane.</span><span class="sxs-lookup"><span data-stu-id="9882f-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="9882f-236">Wyświetl listę nazw bibliotek, które są buforowane.</span><span class="sxs-lookup"><span data-stu-id="9882f-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="9882f-237">Przykłady</span><span class="sxs-lookup"><span data-stu-id="9882f-237">Examples</span></span>

* <span data-ttu-id="9882f-238">Aby wyświetlić nazwy buforowanych bibliotek na dostawcę, użyj jednego z następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="9882f-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="9882f-239">Wyświetlane są dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="9882f-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="9882f-240">Aby wyświetlić nazwy plików bibliotek w pamięci podręcznej na dostawcę:</span><span class="sxs-lookup"><span data-stu-id="9882f-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="9882f-241">Wyświetlane są dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="9882f-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="9882f-242">Zwróć uwagę, że powyższe dane wyjściowe pokazują, że jQuery w wersji 3.2.1 i 3.3.1 są buforowane w ramach dostawcy CDNJS.</span><span class="sxs-lookup"><span data-stu-id="9882f-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="9882f-243">Aby opróżnić pamięć podręczną biblioteki dla dostawcy CDNJS:</span><span class="sxs-lookup"><span data-stu-id="9882f-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="9882f-244">Po opróżnieniu pamięci podręcznej dostawcy CDNJS `libman cache list` polecenie wyświetla następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="9882f-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="9882f-245">Aby opróżnić pamięć podręczną dla wszystkich obsługiwanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="9882f-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="9882f-246">Po opróżnieniu wszystkich pamięci podręcznych dostawcy `libman cache list` polecenie wyświetla następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="9882f-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="9882f-247">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9882f-247">Additional resources</span></span>

* [<span data-ttu-id="9882f-248">Instalowanie narzędzia globalnego</span><span class="sxs-lookup"><span data-stu-id="9882f-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="9882f-249">Repozytorium GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="9882f-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
