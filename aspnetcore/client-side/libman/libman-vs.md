---
title: Używanie LibMan z ASP.NET Core w programie Visual Studio
author: scottaddie
description: Dowiedz się, jak używać LibMan w projekcie ASP.NET Core z programem Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: ae2bc34edaea2df6f329e47b00c7c02cc59d03bd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589273"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="d996a-103">Używanie LibMan z ASP.NET Core w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d996a-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="d996a-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d996a-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d996a-105">Program Visual Studio ma wbudowaną obsługę [LibMan](xref:client-side/libman/index) w projektach ASP.NET Core, w tym:</span><span class="sxs-lookup"><span data-stu-id="d996a-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="d996a-106">Obsługa konfigurowania i uruchamiania operacji przywracania LibMan podczas kompilacji.</span><span class="sxs-lookup"><span data-stu-id="d996a-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="d996a-107">Elementy menu służące do wyzwalania operacji przywracania i czyszczenia LibMan.</span><span class="sxs-lookup"><span data-stu-id="d996a-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="d996a-108">Okno dialogowe wyszukiwania służące do znajdowania bibliotek i dodawania plików do projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="d996a-109">Edytowanie obsługi *libman.jsw* &mdash; pliku manifestu LibMan.</span><span class="sxs-lookup"><span data-stu-id="d996a-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="d996a-110">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/client-side/libman/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d996a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d996a-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="d996a-111">Prerequisites</span></span>

* <span data-ttu-id="d996a-112">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="d996a-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="d996a-113">Dodaj pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="d996a-113">Add library files</span></span>

<span data-ttu-id="d996a-114">Pliki bibliotek można dodać do projektu ASP.NET Core na dwa różne sposoby:</span><span class="sxs-lookup"><span data-stu-id="d996a-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="d996a-115">Korzystanie z okna dialogowego Dodawanie biblioteki Client-Side</span><span class="sxs-lookup"><span data-stu-id="d996a-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="d996a-116">Ręczne konfigurowanie wpisów pliku manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="d996a-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="d996a-117">Korzystanie z okna dialogowego Dodawanie biblioteki Client-Side</span><span class="sxs-lookup"><span data-stu-id="d996a-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="d996a-118">Wykonaj następujące kroki, aby zainstalować bibliotekę po stronie klienta:</span><span class="sxs-lookup"><span data-stu-id="d996a-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="d996a-119">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder projektu, w którym należy dodać pliki.</span><span class="sxs-lookup"><span data-stu-id="d996a-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="d996a-120">Wybierz pozycję **Dodaj**  >  **bibliotekę po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="d996a-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="d996a-121">Zostanie wyświetlone okno dialogowe **dodawanie Client-Side biblioteki** :</span><span class="sxs-lookup"><span data-stu-id="d996a-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Okno dialogowe Dodawanie biblioteki Client-Side](_static/add-library-dialog.png)

* <span data-ttu-id="d996a-123">Wybierz dostawcę biblioteki z listy rozwijanej **dostawca** .</span><span class="sxs-lookup"><span data-stu-id="d996a-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="d996a-124">CDNJS jest dostawcą domyślnym.</span><span class="sxs-lookup"><span data-stu-id="d996a-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="d996a-125">Wpisz nazwę biblioteki do pobrania w polu tekstowym **Biblioteka** .</span><span class="sxs-lookup"><span data-stu-id="d996a-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="d996a-126">Technologia IntelliSense udostępnia listę bibliotek zaczynających się od podanego tekstu.</span><span class="sxs-lookup"><span data-stu-id="d996a-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="d996a-127">Wybierz bibliotekę z listy IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="d996a-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="d996a-128">Zwróć uwagę, że nazwa biblioteki jest poddana sufiksu `@` i najnowszej wersji stabilnej znanej dla wybranego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="d996a-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="d996a-129">Wybieranie plików do uwzględnienia:</span><span class="sxs-lookup"><span data-stu-id="d996a-129">Decide which files to include:</span></span>
  * <span data-ttu-id="d996a-130">Zaznacz przycisk radiowy **Dołącz wszystkie pliki bibliotek** , aby uwzględnić wszystkie pliki biblioteki.</span><span class="sxs-lookup"><span data-stu-id="d996a-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="d996a-131">Wybierz przycisk radiowy **Wybierz określone pliki** , aby dołączyć podzestaw plików biblioteki.</span><span class="sxs-lookup"><span data-stu-id="d996a-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="d996a-132">Po wybraniu przycisku radiowego drzewo selektora plików jest włączone.</span><span class="sxs-lookup"><span data-stu-id="d996a-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="d996a-133">Zaznacz pola po lewej stronie nazw plików do pobrania.</span><span class="sxs-lookup"><span data-stu-id="d996a-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="d996a-134">Określ folder projektu do przechowywania plików w polu tekstowym **Lokalizacja docelowa** .</span><span class="sxs-lookup"><span data-stu-id="d996a-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="d996a-135">Zgodnie z zaleceniami należy przechowywać każdą bibliotekę w osobnym folderze.</span><span class="sxs-lookup"><span data-stu-id="d996a-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="d996a-136">Sugerowany folder **lokalizacji docelowej** jest oparty na lokalizacji, w której uruchomiono okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="d996a-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="d996a-137">Jeśli jest uruchamiany z poziomu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="d996a-137">If launched from the project root:</span></span>
    * <span data-ttu-id="d996a-138">plik *wwwroot/lib* jest używany, jeśli istnieje plik *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="d996a-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="d996a-139">*Biblioteka lib* jest używana, jeśli plik *wwwroot* nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="d996a-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="d996a-140">W przypadku uruchomienia z folderu projektu zostanie użyta odpowiednia nazwa folderu.</span><span class="sxs-lookup"><span data-stu-id="d996a-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="d996a-141">Sugestia folderu jest sufiksem z nazwą biblioteki.</span><span class="sxs-lookup"><span data-stu-id="d996a-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="d996a-142">W poniższej tabeli przedstawiono sugestie dotyczące folderów podczas instalowania jQuery w Razor projekcie stron.</span><span class="sxs-lookup"><span data-stu-id="d996a-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="d996a-143">Lokalizacja uruchamiania</span><span class="sxs-lookup"><span data-stu-id="d996a-143">Launch location</span></span>                           |<span data-ttu-id="d996a-144">Sugerowany folder</span><span class="sxs-lookup"><span data-stu-id="d996a-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="d996a-145">Katalog główny projektu (Jeśli folder *wwwroot* istnieje)</span><span class="sxs-lookup"><span data-stu-id="d996a-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="d996a-146">*wwwroot/lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="d996a-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="d996a-147">Katalog główny projektu (Jeśli folder *wwwroot* nie istnieje)</span><span class="sxs-lookup"><span data-stu-id="d996a-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="d996a-148">*lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="d996a-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="d996a-149">Folder *stron* w projekcie</span><span class="sxs-lookup"><span data-stu-id="d996a-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="d996a-150">*Strony/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="d996a-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="d996a-151">Kliknij przycisk **Zainstaluj** , aby pobrać pliki, na konfigurację w *libman.jsna*.</span><span class="sxs-lookup"><span data-stu-id="d996a-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="d996a-152">Zapoznaj się z informacjami dotyczącymi instalacji w oknie **danych wyjściowych** programu **Library Manager** .</span><span class="sxs-lookup"><span data-stu-id="d996a-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="d996a-153">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d996a-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="d996a-154">Ręczne konfigurowanie wpisów pliku manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="d996a-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="d996a-155">Wszystkie operacje LibMan w programie Visual Studio opierają się na zawartości manifestu LibMan głównego projektu (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="d996a-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="d996a-156">Możesz ręcznie edytować *libman.jsw* celu skonfigurowania plików biblioteki dla projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="d996a-157">Program Visual Studio przywraca wszystkie pliki bibliotek, gdy *libman.json* jest zapisywany.</span><span class="sxs-lookup"><span data-stu-id="d996a-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="d996a-158">Aby otworzyć *libman.jsna* potrzeby edycji, dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="d996a-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="d996a-159">Kliknij dwukrotnie *libman.jsw* pliku w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="d996a-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="d996a-160">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **zarządzaj bibliotekami Client-Side**.</span><span class="sxs-lookup"><span data-stu-id="d996a-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="d996a-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="d996a-161">**&#8224;**</span></span>
* <span data-ttu-id="d996a-162">Wybierz pozycję **Zarządzaj bibliotekami Client-Side** z menu **projektu** programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d996a-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="d996a-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="d996a-163">**&#8224;**</span></span>

<span data-ttu-id="d996a-164">**&#8224;** Jeśli *libman.jsw* pliku nie istnieje już w katalogu głównym projektu, zostanie utworzony z domyślną zawartością szablonu elementu.</span><span class="sxs-lookup"><span data-stu-id="d996a-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="d996a-165">Program Visual Studio oferuje zaawansowane funkcje edycji JSON, takie jak kolorowanie, formatowanie, IntelliSense i walidacja schematu.</span><span class="sxs-lookup"><span data-stu-id="d996a-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="d996a-166">Schemat JSON manifestu LibMan został znaleziony w lokalizacji [https://json.schemastore.org/libman](https://json.schemastore.org/libman) .</span><span class="sxs-lookup"><span data-stu-id="d996a-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="d996a-167">Przy użyciu następującego pliku manifestu LibMan pobiera pliki zgodnie z konfiguracją zdefiniowaną we `libraries` właściwości.</span><span class="sxs-lookup"><span data-stu-id="d996a-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="d996a-168">Wyjaśnienie literałów obiektów zdefiniowanych w `libraries` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="d996a-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="d996a-169">Podzestaw [jQuery](https://jquery.com/) w wersji 3.3.1 jest pobierany z dostawcy CDNJS.</span><span class="sxs-lookup"><span data-stu-id="d996a-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="d996a-170">Podzestaw jest zdefiniowany we `files` właściwościach &mdash; *jquery.min.js*, *jquery.js* i *jQuery. min. map*.</span><span class="sxs-lookup"><span data-stu-id="d996a-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="d996a-171">Pliki są umieszczane w folderze *wwwroot/lib/jQuery* projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="d996a-172">W [całości wersja 4.1.3](https://getbootstrap.com/) jest pobierana i umieszczana w folderze *wwwroot/lib/Bootstrap* .</span><span class="sxs-lookup"><span data-stu-id="d996a-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="d996a-173">Właściwość literału obiektu `provider` zastępuje `defaultProvider` wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="d996a-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="d996a-174">LibMan pobiera pliki Bootstrap z dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="d996a-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="d996a-175">Podzbiór [Lodash](https://lodash.com/) został zatwierdzony przez organ regulujący w organizacji.</span><span class="sxs-lookup"><span data-stu-id="d996a-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="d996a-176">Pliki *lodash.js* i *lodash.min.js* są pobierane z lokalnego systemu plików w lokalizacji *C: \\ temp \\ lodash \\*.</span><span class="sxs-lookup"><span data-stu-id="d996a-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="d996a-177">Pliki są kopiowane do folderu *wwwroot/lib/lodash* projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="d996a-178">LibMan obsługuje tylko jedną wersję każdej biblioteki z każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="d996a-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="d996a-179">Sprawdzanie poprawności schematu *libman.jsw* pliku kończy się niepowodzeniem, jeśli zawiera dwie biblioteki o tej samej nazwie biblioteki dla danego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="d996a-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="d996a-180">Przywróć pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="d996a-180">Restore library files</span></span>

<span data-ttu-id="d996a-181">Aby przywrócić pliki bibliotek z programu Visual Studio, w katalogu głównym projektu musi znajdować się prawidłowy *libman.js* .</span><span class="sxs-lookup"><span data-stu-id="d996a-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="d996a-182">Przywrócone pliki są umieszczane w projekcie w lokalizacji określonej dla każdej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="d996a-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="d996a-183">Pliki bibliotek można przywrócić w projekcie ASP.NET Core na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="d996a-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="d996a-184">Przywróć pliki podczas kompilacji</span><span class="sxs-lookup"><span data-stu-id="d996a-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="d996a-185">Ręczne przywracanie plików</span><span class="sxs-lookup"><span data-stu-id="d996a-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="d996a-186">Przywróć pliki podczas kompilacji</span><span class="sxs-lookup"><span data-stu-id="d996a-186">Restore files during build</span></span>

<span data-ttu-id="d996a-187">LibMan może przywrócić zdefiniowane pliki biblioteki w ramach procesu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="d996a-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="d996a-188">Domyślnie zachowanie funkcji *Przywróć przy kompilacji* jest wyłączone.</span><span class="sxs-lookup"><span data-stu-id="d996a-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="d996a-189">Aby włączyć i przetestować zachowanie funkcji przywracania po kompilacji:</span><span class="sxs-lookup"><span data-stu-id="d996a-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="d996a-190">Kliknij prawym przyciskiem myszy pozycję *libman.jsna* **Eksplorator rozwiązań** , a następnie wybierz pozycję **Włącz przywracanie bibliotek Client-Side w kompilacji** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="d996a-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="d996a-191">Po wyświetleniu monitu o zainstalowanie pakietu NuGet kliknij przycisk **tak** .</span><span class="sxs-lookup"><span data-stu-id="d996a-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="d996a-192">Pakiet NuGet [Microsoft. Web. librarymanager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) został dodany do projektu:</span><span class="sxs-lookup"><span data-stu-id="d996a-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="d996a-193">Skompiluj projekt, aby upewnić się, że następuje przywracanie pliku LibMan.</span><span class="sxs-lookup"><span data-stu-id="d996a-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="d996a-194">`Microsoft.Web.LibraryManager.Build`Pakiet wstrzyknąć obiekt docelowy MSBuild, który uruchamia LibMan podczas operacji kompilowania projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="d996a-195">Przejrzyj źródło danych **kompilacji** w oknie **danych wyjściowych** dla dziennika aktywności LibMan:</span><span class="sxs-lookup"><span data-stu-id="d996a-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="d996a-196">Gdy włączone jest zachowanie przywracania po kompilacji, *libman.jsw* menu kontekstowym zostanie wyświetlona opcja **Wyłącz Przywracanie Client-Side podczas kompilacji** .</span><span class="sxs-lookup"><span data-stu-id="d996a-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="d996a-197">Wybranie tej opcji spowoduje usunięcie `Microsoft.Web.LibraryManager.Build` odwołania do pakietu z pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="d996a-198">W związku z tym biblioteki po stronie klienta nie są już przywracane dla każdej kompilacji.</span><span class="sxs-lookup"><span data-stu-id="d996a-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="d996a-199">Bez względu na ustawienie Przywróć na kompilację można ręcznie przywrócić w dowolnym momencie z *libman.jsw* menu kontekstowym.</span><span class="sxs-lookup"><span data-stu-id="d996a-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="d996a-200">Aby uzyskać więcej informacji, zobacz [Przywracanie plików ręcznie](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="d996a-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="d996a-201">Ręczne przywracanie plików</span><span class="sxs-lookup"><span data-stu-id="d996a-201">Restore files manually</span></span>

<span data-ttu-id="d996a-202">Aby ręcznie przywrócić pliki biblioteki:</span><span class="sxs-lookup"><span data-stu-id="d996a-202">To manually restore library files:</span></span>

* <span data-ttu-id="d996a-203">Dla wszystkich projektów w rozwiązaniu:</span><span class="sxs-lookup"><span data-stu-id="d996a-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="d996a-204">Kliknij prawym przyciskiem myszy nazwę rozwiązania w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="d996a-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="d996a-205">Wybierz opcję **Przywróć biblioteki Client-Side** .</span><span class="sxs-lookup"><span data-stu-id="d996a-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="d996a-206">Dla określonego projektu:</span><span class="sxs-lookup"><span data-stu-id="d996a-206">For a specific project:</span></span>
  * <span data-ttu-id="d996a-207">Kliknij prawym przyciskiem myszy *libman.jsw* pliku w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="d996a-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="d996a-208">Wybierz opcję **Przywróć biblioteki Client-Side** .</span><span class="sxs-lookup"><span data-stu-id="d996a-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="d996a-209">Podczas gdy operacja przywracania jest uruchomiona:</span><span class="sxs-lookup"><span data-stu-id="d996a-209">While the restore operation is running:</span></span>

* <span data-ttu-id="d996a-210">Ikona centrum stanu zadań (TSC) na pasku stanu programu Visual Studio zostanie animowana i zostanie *rozpoczęta operacja przywracania*.</span><span class="sxs-lookup"><span data-stu-id="d996a-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="d996a-211">Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającego listę znanych zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="d996a-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="d996a-212">Komunikaty będą wysyłane do paska stanu i źródła danych programu **Library Manager** okna **danych wyjściowych** .</span><span class="sxs-lookup"><span data-stu-id="d996a-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="d996a-213">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d996a-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="d996a-214">Usuń pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="d996a-214">Delete library files</span></span>

<span data-ttu-id="d996a-215">Aby wykonać operację *czyszczenia* , która spowoduje usunięcie plików biblioteki, które zostały wcześniej przywrócone w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d996a-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="d996a-216">Kliknij prawym przyciskiem myszy *libman.jsw* pliku w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="d996a-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="d996a-217">Wybierz opcję **wyczyść Client-Side biblioteki** .</span><span class="sxs-lookup"><span data-stu-id="d996a-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="d996a-218">Aby zapobiec przypadkowemu usunięciu plików nienależących do biblioteki, operacja czyszczenia nie usuwa całych katalogów.</span><span class="sxs-lookup"><span data-stu-id="d996a-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="d996a-219">Usuwa tylko te pliki, które zostały uwzględnione w poprzednim przywracaniu.</span><span class="sxs-lookup"><span data-stu-id="d996a-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="d996a-220">Podczas gdy operacja czyszczenia jest uruchomiona:</span><span class="sxs-lookup"><span data-stu-id="d996a-220">While the clean operation is running:</span></span>

* <span data-ttu-id="d996a-221">Ikona TSC na pasku stanu programu Visual Studio będzie animowana i zostanie *rozpoczęta operacja odczytywania bibliotek klienckich*.</span><span class="sxs-lookup"><span data-stu-id="d996a-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="d996a-222">Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającego listę znanych zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="d996a-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="d996a-223">Komunikaty są wysyłane do paska stanu i źródła danych programu **Library Manager** okna **danych wyjściowych** .</span><span class="sxs-lookup"><span data-stu-id="d996a-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="d996a-224">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d996a-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="d996a-225">Operacja czyszczenia usuwa tylko pliki z projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="d996a-226">Pliki biblioteki znajdują się w pamięci podręcznej w celu szybszego pobierania podczas przyszłych operacji przywracania.</span><span class="sxs-lookup"><span data-stu-id="d996a-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="d996a-227">Aby zarządzać plikami biblioteki przechowywanymi w pamięci podręcznej komputera lokalnego, użyj [interfejsu wiersza polecenia LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="d996a-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="d996a-228">Odinstaluj pliki biblioteki</span><span class="sxs-lookup"><span data-stu-id="d996a-228">Uninstall library files</span></span>

<span data-ttu-id="d996a-229">Aby odinstalować pliki biblioteki:</span><span class="sxs-lookup"><span data-stu-id="d996a-229">To uninstall library files:</span></span>

* <span data-ttu-id="d996a-230">Otwórz *libman.jsna*.</span><span class="sxs-lookup"><span data-stu-id="d996a-230">Open *libman.json*.</span></span>
* <span data-ttu-id="d996a-231">Umieść karetkę wewnątrz odpowiedniego `libraries` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="d996a-232">Kliknij ikonę żarówki, która pojawia się na lewym marginesie, a następnie wybierz pozycję **Odinstaluj \<library_name> @ \<library_version>**:</span><span class="sxs-lookup"><span data-stu-id="d996a-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Opcja menu kontekstowego odinstalowywania biblioteki](_static/uninstall-menu-option.png)

<span data-ttu-id="d996a-234">Alternatywnie można ręcznie edytować i zapisać manifest LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="d996a-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="d996a-235">[Operacja przywracania](#restore-library-files) jest uruchamiana, gdy plik zostanie zapisany.</span><span class="sxs-lookup"><span data-stu-id="d996a-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="d996a-236">Pliki bibliotek, które nie są już zdefiniowane w *libman.jsna* są usuwane z projektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="d996a-237">Zaktualizuj wersję biblioteki</span><span class="sxs-lookup"><span data-stu-id="d996a-237">Update library version</span></span>

<span data-ttu-id="d996a-238">Aby sprawdzić dostępność zaktualizowanej wersji biblioteki:</span><span class="sxs-lookup"><span data-stu-id="d996a-238">To check for an updated library version:</span></span>

* <span data-ttu-id="d996a-239">Otwórz *libman.jsna*.</span><span class="sxs-lookup"><span data-stu-id="d996a-239">Open *libman.json*.</span></span>
* <span data-ttu-id="d996a-240">Umieść karetkę wewnątrz odpowiedniego `libraries` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="d996a-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="d996a-241">Kliknij ikonę żarówki, która pojawia się na lewym marginesie.</span><span class="sxs-lookup"><span data-stu-id="d996a-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="d996a-242">Umieść kursor nad **sprawdzaniem dostępności aktualizacji**.</span><span class="sxs-lookup"><span data-stu-id="d996a-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="d996a-243">LibMan sprawdza, czy wersja biblioteki jest nowsza niż zainstalowana wersja.</span><span class="sxs-lookup"><span data-stu-id="d996a-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="d996a-244">Mogą wystąpić następujące wyniki:</span><span class="sxs-lookup"><span data-stu-id="d996a-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="d996a-245">Jeśli Najnowsza wersja jest już zainstalowana, zostanie wyświetlony komunikat " **nie znaleziono aktualizacji** ".</span><span class="sxs-lookup"><span data-stu-id="d996a-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="d996a-246">Najnowsza stabilna wersja jest wyświetlana, jeśli nie została jeszcze zainstalowana.</span><span class="sxs-lookup"><span data-stu-id="d996a-246">The latest stable version is displayed if not already installed.</span></span>

  ![Opcja menu kontekstowego wyszukiwania aktualizacji](_static/update-menu-option.png)

* <span data-ttu-id="d996a-248">Jeśli dostępna jest wersja wstępna nowsza niż zainstalowana, zostanie wyświetlona wersja wstępna.</span><span class="sxs-lookup"><span data-stu-id="d996a-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="d996a-249">Aby zmienić wersję na starszą dla starszej wersji biblioteki, ręcznie Edytuj *libman.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="d996a-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="d996a-250">Po zapisaniu pliku LibMan [operacji przywracania](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="d996a-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="d996a-251">Usuwa nadmiarowe pliki z poprzedniej wersji.</span><span class="sxs-lookup"><span data-stu-id="d996a-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="d996a-252">Dodaje nowe i zaktualizowane pliki z nowej wersji.</span><span class="sxs-lookup"><span data-stu-id="d996a-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d996a-253">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="d996a-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="d996a-254">Repozytorium GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="d996a-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
