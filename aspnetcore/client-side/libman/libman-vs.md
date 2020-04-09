---
title: Używanie programu LibMan z ASP.NET Core w programie Visual Studio
author: scottaddie
description: Dowiedz się, jak używać programu LibMan w projekcie ASP.NET Core w programie Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658313"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="6fe77-103">Używanie programu LibMan z ASP.NET Core w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6fe77-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="6fe77-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6fe77-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6fe77-105">Visual Studio ma wbudowaną obsługę [libman](xref:client-side/libman/index) w projektach ASP.NET Core, w tym:</span><span class="sxs-lookup"><span data-stu-id="6fe77-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="6fe77-106">Obsługa konfigurowania i uruchamiania operacji przywracania LibMan na kompilacji.</span><span class="sxs-lookup"><span data-stu-id="6fe77-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="6fe77-107">Elementy menu do wyzwalania libman przywracania i czyste operacje.</span><span class="sxs-lookup"><span data-stu-id="6fe77-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="6fe77-108">Wyszukaj okno dialogowe znajdowania bibliotek i dodawania plików do projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="6fe77-109">Obsługa edycji pliku manifestu *libman.json.*&mdash;</span><span class="sxs-lookup"><span data-stu-id="6fe77-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="6fe77-110">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6fe77-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6fe77-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6fe77-111">Prerequisites</span></span>

* <span data-ttu-id="6fe77-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="6fe77-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="6fe77-113">Dodawanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="6fe77-113">Add library files</span></span>

<span data-ttu-id="6fe77-114">Pliki bibliotek można dodawać do projektu ASP.NET Core na dwa różne sposoby:</span><span class="sxs-lookup"><span data-stu-id="6fe77-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="6fe77-115">Okno dialogowe Dodawanie biblioteki po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="6fe77-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="6fe77-116">Ręczne konfigurowanie wpisów plików manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="6fe77-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="6fe77-117">Okno dialogowe Dodawanie biblioteki po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="6fe77-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="6fe77-118">Aby zainstalować bibliotekę po stronie klienta, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="6fe77-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="6fe77-119">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder projektu, w którym należy dodać pliki.</span><span class="sxs-lookup"><span data-stu-id="6fe77-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="6fe77-120">Wybierz **pozycję Dodaj** > **bibliotekę po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="6fe77-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="6fe77-121">Zostanie wyświetlone okno dialogowe **Dodaj bibliotekę po stronie klienta:**</span><span class="sxs-lookup"><span data-stu-id="6fe77-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta](_static/add-library-dialog.png)

* <span data-ttu-id="6fe77-123">Wybierz dostawcę biblioteki z listy rozwijanej **Dostawca.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="6fe77-124">Usługa CDNJS jest dostawcą domyślnym.</span><span class="sxs-lookup"><span data-stu-id="6fe77-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="6fe77-125">Wpisz nazwę biblioteki do pobrania w polu tekstowym **Biblioteka.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="6fe77-126">IntelliSense udostępnia listę bibliotek, począwszy od podanego tekstu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="6fe77-127">Wybierz bibliotekę z listy IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="6fe77-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="6fe77-128">Zwróć uwagę, że nazwa biblioteki `@` jest sufiksem z symbolem i najnowszą stabilną wersją znaną wybranemu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="6fe77-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="6fe77-129">Zdecyduj, które pliki mają być dołączane:</span><span class="sxs-lookup"><span data-stu-id="6fe77-129">Decide which files to include:</span></span>
  * <span data-ttu-id="6fe77-130">Wybierz przycisk opcji **Dołącz wszystkie pliki biblioteki,** aby uwzględnić wszystkie pliki biblioteki.</span><span class="sxs-lookup"><span data-stu-id="6fe77-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="6fe77-131">Wybierz przycisk opcji **Wybierz określone pliki,** aby dołączyć podzbiór plików biblioteki.</span><span class="sxs-lookup"><span data-stu-id="6fe77-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="6fe77-132">Po wybraniu przycisku opcji jest włączone drzewo selektora plików.</span><span class="sxs-lookup"><span data-stu-id="6fe77-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="6fe77-133">Zaznacz pola po lewej stronie nazw plików do pobrania.</span><span class="sxs-lookup"><span data-stu-id="6fe77-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="6fe77-134">Określ folder projektu do przechowywania plików w polu **tekstowym Lokalizacja docelowa.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="6fe77-135">Jako zalecenie należy przechowywać każdą bibliotekę w osobnym folderze.</span><span class="sxs-lookup"><span data-stu-id="6fe77-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="6fe77-136">Sugerowany folder **Lokalizacja docelowa** jest oparty na lokalizacji, z której uruchomiono okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="6fe77-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="6fe77-137">Jeśli zostanie uruchomiony z katalogu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="6fe77-137">If launched from the project root:</span></span>
    * <span data-ttu-id="6fe77-138">*wwwroot/lib* jest używany, jeśli *wwwroot* istnieje.</span><span class="sxs-lookup"><span data-stu-id="6fe77-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="6fe77-139">*lib* jest używany, jeśli *wwwroot* nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="6fe77-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="6fe77-140">Jeśli zostanie uruchomiony z folderu projektu, używana jest odpowiednia nazwa folderu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="6fe77-141">Sugestia folderu jest sufiksem z nazwą biblioteki.</span><span class="sxs-lookup"><span data-stu-id="6fe77-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="6fe77-142">W poniższej tabeli przedstawiono sugestie folderów podczas instalowania jQuery w projekcie Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6fe77-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="6fe77-143">Miejsce uruchomienia</span><span class="sxs-lookup"><span data-stu-id="6fe77-143">Launch location</span></span>                           |<span data-ttu-id="6fe77-144">Sugerowany folder</span><span class="sxs-lookup"><span data-stu-id="6fe77-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="6fe77-145">główny projekt (jeśli *wwwroot* istnieje)</span><span class="sxs-lookup"><span data-stu-id="6fe77-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="6fe77-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="6fe77-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="6fe77-147">główny projekt (jeśli *wwwroot* nie istnieje)</span><span class="sxs-lookup"><span data-stu-id="6fe77-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="6fe77-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="6fe77-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="6fe77-149">*Folder Strony* w projekcie</span><span class="sxs-lookup"><span data-stu-id="6fe77-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="6fe77-150">*Strony/jquery/*</span><span class="sxs-lookup"><span data-stu-id="6fe77-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="6fe77-151">Kliknij przycisk **Zainstaluj,** aby pobrać pliki, według konfiguracji w *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="6fe77-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="6fe77-152">Przejrzyj kanał informacyjny **Menedżera bibliotek** okna **Dane wyjściowe,** aby uzyskać szczegółowe informacje na temat instalacji.</span><span class="sxs-lookup"><span data-stu-id="6fe77-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="6fe77-153">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6fe77-153">For example:</span></span>

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

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="6fe77-154">Ręczne konfigurowanie wpisów plików manifestu LibMan</span><span class="sxs-lookup"><span data-stu-id="6fe77-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="6fe77-155">Wszystkie operacje LibMana w programie Visual Studio są oparte na zawartości manifestu LibMan głównego projektu (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="6fe77-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="6fe77-156">Można ręcznie edytować *libman.json,* aby skonfigurować pliki biblioteki dla projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="6fe77-157">Program Visual Studio przywraca wszystkie pliki biblioteki po *zapisaniu pliku libman.json.*</span><span class="sxs-lookup"><span data-stu-id="6fe77-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="6fe77-158">Aby otworzyć *libman.json* do edycji, istnieją następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="6fe77-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="6fe77-159">Kliknij dwukrotnie plik *libman.json* w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="6fe77-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="6fe77-160">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Zarządzaj bibliotekami po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="6fe77-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="6fe77-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="6fe77-161">**&#8224;**</span></span>
* <span data-ttu-id="6fe77-162">Z menu Programu Visual Studio **Project** wybierz polecenie **Zarządzaj bibliotekami po stronie klienta.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="6fe77-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="6fe77-163">**&#8224;**</span></span>

<span data-ttu-id="6fe77-164">**&#8224;** Jeśli plik *libman.json* jeszcze nie istnieje w katalogu głównym projektu, zostanie utworzony przy zachowaniu domyślnej zawartości szablonu elementu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="6fe77-165">Program Visual Studio oferuje bogatą obsługę edycji JSON, taką jak kolorowanie, formatowanie, intellisense i sprawdzanie poprawności schematu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="6fe77-166">Schemat JSON manifestu LibMana znajduje [https://json.schemastore.org/libman](https://json.schemastore.org/libman)się w pliku .</span><span class="sxs-lookup"><span data-stu-id="6fe77-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="6fe77-167">Z następującym plikiem manifestu LibMan pobiera pliki `libraries` dla konfiguracji zdefiniowanej we właściwości.</span><span class="sxs-lookup"><span data-stu-id="6fe77-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="6fe77-168">Wyjaśnienie literałów obiektu zdefiniowanych `libraries` w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="6fe77-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="6fe77-169">Podzbiór [jQuery](https://jquery.com/) w wersji 3.3.1 jest pobierany od dostawcy usługi CDNJS.</span><span class="sxs-lookup"><span data-stu-id="6fe77-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="6fe77-170">Podzbiór jest `files` zdefiniowany w właściwości&mdash;*jquery.min.js*, *jquery.js*i *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="6fe77-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="6fe77-171">Pliki są umieszczane w folderze *wwwroot/lib/jquery* projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="6fe77-172">Całość wersji 4.1.3 [Bootstrap](https://getbootstrap.com/) jest pobierana i umieszczana w folderze *wwwroot/lib/bootstrap.*</span><span class="sxs-lookup"><span data-stu-id="6fe77-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="6fe77-173">`provider` Właściwość dosłownego obiektu zastępuje `defaultProvider` wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="6fe77-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="6fe77-174">LibMan pobiera pliki Bootstrap z dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="6fe77-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="6fe77-175">Podzbiór [Lodash](https://lodash.com/) został zatwierdzony przez organ zarządzający w organizacji.</span><span class="sxs-lookup"><span data-stu-id="6fe77-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="6fe77-176">Pliki *lodash.js* i *lodash.min.js* są pobierane z lokalnego systemu plików w *C:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="6fe77-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="6fe77-177">Pliki są kopiowane do folderu *wwwroot/lib/lodash* projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="6fe77-178">LibMan obsługuje tylko jedną wersję każdej biblioteki od każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="6fe77-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="6fe77-179">Plik *libman.json* nie zostanie zawiedzieny schematu, jeśli zawiera dwie biblioteki o tej samej nazwie biblioteki dla danego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="6fe77-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="6fe77-180">Przywracanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="6fe77-180">Restore library files</span></span>

<span data-ttu-id="6fe77-181">Aby przywrócić pliki biblioteki z poziomu programu Visual Studio, w katalogu głównym projektu musi znajdować się prawidłowy plik *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="6fe77-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="6fe77-182">Przywrócone pliki są umieszczane w projekcie w lokalizacji określonej dla każdej biblioteki.</span><span class="sxs-lookup"><span data-stu-id="6fe77-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="6fe77-183">Pliki bibliotek można przywrócić w projekcie ASP.NET Core na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="6fe77-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="6fe77-184">Przywracanie plików podczas kompilacji</span><span class="sxs-lookup"><span data-stu-id="6fe77-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="6fe77-185">Ręczne przywracanie plików</span><span class="sxs-lookup"><span data-stu-id="6fe77-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="6fe77-186">Przywracanie plików podczas kompilacji</span><span class="sxs-lookup"><span data-stu-id="6fe77-186">Restore files during build</span></span>

<span data-ttu-id="6fe77-187">LibMan można przywrócić zdefiniowane pliki biblioteki w ramach procesu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="6fe77-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="6fe77-188">Domyślnie zachowanie *przywracania na kompilacji* jest wyłączone.</span><span class="sxs-lookup"><span data-stu-id="6fe77-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="6fe77-189">Aby włączyć i przetestować zachowanie przywracania przy kompilacji:</span><span class="sxs-lookup"><span data-stu-id="6fe77-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="6fe77-190">Kliknij prawym przyciskiem myszy *libman.json* w **Eksploratorze rozwiązań** i wybierz **polecenie Włącz przywracanie bibliotek po stronie klienta w menu** kontekstowym.</span><span class="sxs-lookup"><span data-stu-id="6fe77-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="6fe77-191">Kliknij przycisk **Tak** po wyświetleniu monitu o zainstalowanie pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="6fe77-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="6fe77-192">Pakiet [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet jest dodawany do projektu:</span><span class="sxs-lookup"><span data-stu-id="6fe77-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="6fe77-193">Tworzenie projektu, aby potwierdzić przywrócenie pliku LibMan występuje.</span><span class="sxs-lookup"><span data-stu-id="6fe77-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="6fe77-194">Pakiet `Microsoft.Web.LibraryManager.Build` wstrzykuje obiekt docelowy MSBuild, który uruchamia LibMan podczas operacji kompilacji projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="6fe77-195">Przejrzyj **źródło** danych kompilacji w oknie **dane wyjściowe** dla dziennika aktywności LibMan:</span><span class="sxs-lookup"><span data-stu-id="6fe77-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

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

<span data-ttu-id="6fe77-196">Gdy zachowanie przywracania na kompilacji jest włączone, w menu kontekstowym *libman.json* jest wyświetlana opcja **Wyłącz przywracanie bibliotek po stronie klienta w kompilacji.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="6fe77-197">Wybranie tej opcji `Microsoft.Web.LibraryManager.Build` powoduje usunięcie odwołania do pakietu z pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="6fe77-198">W związku z tym biblioteki po stronie klienta nie są już przywracane na każdej kompilacji.</span><span class="sxs-lookup"><span data-stu-id="6fe77-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="6fe77-199">Niezależnie od ustawienia przywracania na kompilacji, można ręcznie przywrócić w dowolnym momencie z menu kontekstowego *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="6fe77-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="6fe77-200">Aby uzyskać więcej informacji, zobacz [Ręczne przywracanie plików](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="6fe77-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="6fe77-201">Ręczne przywracanie plików</span><span class="sxs-lookup"><span data-stu-id="6fe77-201">Restore files manually</span></span>

<span data-ttu-id="6fe77-202">Aby ręcznie przywrócić pliki biblioteki:</span><span class="sxs-lookup"><span data-stu-id="6fe77-202">To manually restore library files:</span></span>

* <span data-ttu-id="6fe77-203">Dla wszystkich projektów w rozwiązaniu:</span><span class="sxs-lookup"><span data-stu-id="6fe77-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="6fe77-204">Kliknij prawym przyciskiem myszy nazwę rozwiązania w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="6fe77-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="6fe77-205">Wybierz opcję **Przywróć biblioteki po stronie klienta.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="6fe77-206">Dla określonego projektu:</span><span class="sxs-lookup"><span data-stu-id="6fe77-206">For a specific project:</span></span>
  * <span data-ttu-id="6fe77-207">Kliknij prawym przyciskiem myszy plik *libman.json* w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="6fe77-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="6fe77-208">Wybierz opcję **Przywróć biblioteki po stronie klienta.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="6fe77-209">Gdy operacja przywracania jest uruchomiona:</span><span class="sxs-lookup"><span data-stu-id="6fe77-209">While the restore operation is running:</span></span>

* <span data-ttu-id="6fe77-210">Ikona Centrum stanu zadań (TSC) na pasku stanu programu Visual Studio zostanie animowana i zostanie uruchomiona *operacja Przywracanie*.</span><span class="sxs-lookup"><span data-stu-id="6fe77-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="6fe77-211">Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającej listę znanych zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="6fe77-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="6fe77-212">Wiadomości będą wysyłane do paska stanu i kanału informacyjnego **Menedżera bibliotek** w oknie **Dane wyjściowe.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="6fe77-213">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6fe77-213">For example:</span></span>

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

## <a name="delete-library-files"></a><span data-ttu-id="6fe77-214">Usuwanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="6fe77-214">Delete library files</span></span>

<span data-ttu-id="6fe77-215">Aby wykonać *czystą* operację, która usuwa pliki biblioteki wcześniej przywrócone w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6fe77-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="6fe77-216">Kliknij prawym przyciskiem myszy plik *libman.json* w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="6fe77-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="6fe77-217">Wybierz opcję **Wyczyść biblioteki po stronie klienta.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="6fe77-218">Aby zapobiec niezamierzonemu usunięciu plików spoza biblioteki, czysta operacja nie powoduje usunięcia całych katalogów.</span><span class="sxs-lookup"><span data-stu-id="6fe77-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="6fe77-219">Usuwa tylko pliki, które zostały uwzględnione w poprzednim przywracaniu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="6fe77-220">Gdy czysta operacja jest uruchomiona:</span><span class="sxs-lookup"><span data-stu-id="6fe77-220">While the clean operation is running:</span></span>

* <span data-ttu-id="6fe77-221">Ikona TSC na pasku stanu programu Visual Studio zostanie animowana i zostanie *uruchomiona operacja bibliotek klienta.*</span><span class="sxs-lookup"><span data-stu-id="6fe77-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="6fe77-222">Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającej listę znanych zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="6fe77-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="6fe77-223">Wiadomości są wysyłane do paska stanu i kanału informacyjnego **Menedżera bibliotek** w oknie **Dane wyjściowe.**</span><span class="sxs-lookup"><span data-stu-id="6fe77-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="6fe77-224">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6fe77-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="6fe77-225">Czysta operacja usuwa tylko pliki z projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="6fe77-226">Pliki biblioteki pozostają w pamięci podręcznej, aby przyspieszyć pobieranie przyszłych operacji przywracania.</span><span class="sxs-lookup"><span data-stu-id="6fe77-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="6fe77-227">Aby zarządzać plikami bibliotek przechowywanymi w pamięci podręcznej komputera lokalnego, należy użyć [interfejsu wiersza polecenia LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="6fe77-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="6fe77-228">Odinstalowywanie plików biblioteki</span><span class="sxs-lookup"><span data-stu-id="6fe77-228">Uninstall library files</span></span>

<span data-ttu-id="6fe77-229">Aby odinstalować pliki biblioteki:</span><span class="sxs-lookup"><span data-stu-id="6fe77-229">To uninstall library files:</span></span>

* <span data-ttu-id="6fe77-230">Otwórz *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="6fe77-230">Open *libman.json*.</span></span>
* <span data-ttu-id="6fe77-231">Umieść cieszę `libraries` wewnątrz odpowiedniego obiektu dosłownego.</span><span class="sxs-lookup"><span data-stu-id="6fe77-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="6fe77-232">Kliknij ikonę żarówki wyświetlaną na lewym marginesie, a następnie wybierz \*\*pozycję Odinstaluj \<library_name>@\<library_version>: \*\*</span><span class="sxs-lookup"><span data-stu-id="6fe77-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Opcja menu kontekstowego biblioteki odinstalowywania](_static/uninstall-menu-option.png)

<span data-ttu-id="6fe77-234">Alternatywnie można ręcznie edytować i zapisywać manifest LibMana (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="6fe77-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="6fe77-235">[Operacja przywracania](#restore-library-files) jest uruchamiana po zapisaniu pliku.</span><span class="sxs-lookup"><span data-stu-id="6fe77-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="6fe77-236">Pliki biblioteki, które nie są już zdefiniowane w *libman.json* są usuwane z projektu.</span><span class="sxs-lookup"><span data-stu-id="6fe77-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="6fe77-237">Aktualizowanie wersji biblioteki</span><span class="sxs-lookup"><span data-stu-id="6fe77-237">Update library version</span></span>

<span data-ttu-id="6fe77-238">Aby sprawdzić dostępność zaktualizowanej wersji biblioteki:</span><span class="sxs-lookup"><span data-stu-id="6fe77-238">To check for an updated library version:</span></span>

* <span data-ttu-id="6fe77-239">Otwórz *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="6fe77-239">Open *libman.json*.</span></span>
* <span data-ttu-id="6fe77-240">Umieść cieszę `libraries` wewnątrz odpowiedniego obiektu dosłownego.</span><span class="sxs-lookup"><span data-stu-id="6fe77-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="6fe77-241">Kliknij ikonę żarówki, która pojawi się na lewym marginesie.</span><span class="sxs-lookup"><span data-stu-id="6fe77-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="6fe77-242">Umieść wskaźnik myszy **na czeku pod kątem aktualizacji**.</span><span class="sxs-lookup"><span data-stu-id="6fe77-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="6fe77-243">LibMan sprawdza, czy wersja biblioteki jest nowsza niż zainstalowana wersja.</span><span class="sxs-lookup"><span data-stu-id="6fe77-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="6fe77-244">Mogą wystąpić następujące wyniki:</span><span class="sxs-lookup"><span data-stu-id="6fe77-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="6fe77-245">Komunikat **Brak znalezienia aktualizacji** jest wyświetlany, jeśli najnowsza wersja jest już zainstalowana.</span><span class="sxs-lookup"><span data-stu-id="6fe77-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="6fe77-246">Najnowsza stabilna wersja jest wyświetlana, jeśli nie jest jeszcze zainstalowana.</span><span class="sxs-lookup"><span data-stu-id="6fe77-246">The latest stable version is displayed if not already installed.</span></span>

  ![Sprawdź, czy opcja menu kontekstowego aktualizacji](_static/update-menu-option.png)

* <span data-ttu-id="6fe77-248">Jeśli dostępna jest wersja wstępna nowsza niż zainstalowana wersja, zostanie wyświetlona wersja wstępna.</span><span class="sxs-lookup"><span data-stu-id="6fe77-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="6fe77-249">Aby przejść na starszą wersję biblioteki, należy ręcznie edytować plik *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="6fe77-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="6fe77-250">Po zapisaniu pliku operacja [przywracania](#restore-library-files)LibMan:</span><span class="sxs-lookup"><span data-stu-id="6fe77-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="6fe77-251">Usuwa nadmiarowe pliki z poprzedniej wersji.</span><span class="sxs-lookup"><span data-stu-id="6fe77-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="6fe77-252">Dodaje nowe i zaktualizowane pliki z nowej wersji.</span><span class="sxs-lookup"><span data-stu-id="6fe77-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6fe77-253">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6fe77-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="6fe77-254">Repozytorium LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="6fe77-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
