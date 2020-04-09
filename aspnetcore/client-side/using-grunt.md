---
title: Użyj Grunt w ASP.NET Core
author: rick-anderson
description: Użyj Grunt w ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657592"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="e9d07-103">Użyj Grunt w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9d07-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="e9d07-104">Grunt to biegacz zadań JavaScript, który automatyzuje tworzenie skryptów, kompilację TypeScript, narzędzia "lint" jakości kodu, preprocesy CSS i prawie każdą powtarzalną pracę, która wymaga działania w celu wspierania rozwoju klienta.</span><span class="sxs-lookup"><span data-stu-id="e9d07-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="e9d07-105">Grunt jest w pełni obsługiwany w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e9d07-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="e9d07-106">W tym przykładzie użyto pustego projektu ASP.NET Core jako punktu wyjścia, aby pokazać, jak zautomatyzować proces kompilacji klienta od podstaw.</span><span class="sxs-lookup"><span data-stu-id="e9d07-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="e9d07-107">Gotowy przykład czyści katalog wdrożenia docelowego, łączy pliki JavaScript, sprawdza jakość kodu, kondensuje zawartość pliku JavaScript i wdraża do katalogu głównego aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="e9d07-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="e9d07-108">Użyjemy następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="e9d07-108">We will use the following packages:</span></span>

* <span data-ttu-id="e9d07-109">**grunt**: Pakiet grunt zadanie runner.</span><span class="sxs-lookup"><span data-stu-id="e9d07-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="e9d07-110">**grunt-contrib-clean**: Wtyczka, która usuwa pliki lub katalogi.</span><span class="sxs-lookup"><span data-stu-id="e9d07-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="e9d07-111">**grunt-contrib-jshint**: Wtyczka, która recenzuje jakość kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e9d07-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="e9d07-112">**grunt-contrib-concat**: Wtyczka, która łączy pliki w jeden plik.</span><span class="sxs-lookup"><span data-stu-id="e9d07-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="e9d07-113">**grunt-contrib-uglify**: Wtyczka, która minifies JavaScript w celu zmniejszenia rozmiaru.</span><span class="sxs-lookup"><span data-stu-id="e9d07-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="e9d07-114">**grunt-contrib-watch**: Wtyczka, która obserwuje aktywność plików.</span><span class="sxs-lookup"><span data-stu-id="e9d07-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="e9d07-115">Przygotowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="e9d07-115">Preparing the application</span></span>

<span data-ttu-id="e9d07-116">Aby rozpocząć, skonfiguruj nową pustą aplikację sieci Web i dodaj przykładowe pliki TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e9d07-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="e9d07-117">Pliki TypeScript są automatycznie kompilowane do JavaScript przy użyciu domyślnych ustawień programu Visual Studio i będą naszym surowcem do przetwarzania za pomocą Grunt.</span><span class="sxs-lookup"><span data-stu-id="e9d07-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="e9d07-118">W programie Visual Studio `ASP.NET Web Application`utwórz nowy program .</span><span class="sxs-lookup"><span data-stu-id="e9d07-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="e9d07-119">W oknie dialogowym **Nowy projekt ASP.NET** wybierz szablon ASP.NET **Opróżnij rdzeń** i kliknij przycisk OK.</span><span class="sxs-lookup"><span data-stu-id="e9d07-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="e9d07-120">W Eksploratorze rozwiązań przejrzyj strukturę projektu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="e9d07-121">Folder `\src` zawiera `wwwroot` puste `Dependencies` i węzły.</span><span class="sxs-lookup"><span data-stu-id="e9d07-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![puste rozwiązanie internetowe](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="e9d07-123">Dodaj nowy folder `TypeScript` o nazwie do katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="e9d07-124">Przed dodaniem jakichkolwiek plików upewnij się, że program Visual Studio ma opcję "skompiluj przy zapisywaniu" dla zaznaczonych plików TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e9d07-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="e9d07-125">**Project**Przejdź do projektu**Typescript** > **Edytora** > tekstu**opcji** >  **narzędzi:** > </span><span class="sxs-lookup"><span data-stu-id="e9d07-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![opcje ustawianie automatycznej kompilacji plików TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="e9d07-127">Kliknij prawym `TypeScript` przyciskiem myszy katalog i wybierz polecenie **Dodaj > Nowy element** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="e9d07-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e9d07-128">Wybierz element **pliku JavaScript** i nazwij plik *Tastes.ts* (zwróć uwagę na \*rozszerzenie .ts).</span><span class="sxs-lookup"><span data-stu-id="e9d07-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="e9d07-129">Skopiuj wiersz kodu TypeScript poniżej do pliku (po zapisaniu pojawi się nowy plik *Tastes.js* ze źródłem JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e9d07-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="e9d07-130">Dodaj drugi plik do katalogu **TypeScript** `Food.ts`i nadaj jego nazwę .</span><span class="sxs-lookup"><span data-stu-id="e9d07-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="e9d07-131">Skopiuj poniższy kod do pliku.</span><span class="sxs-lookup"><span data-stu-id="e9d07-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="e9d07-132">Konfigurowanie serwera NPM</span><span class="sxs-lookup"><span data-stu-id="e9d07-132">Configuring NPM</span></span>

<span data-ttu-id="e9d07-133">Następnie skonfiguruj NPM, aby pobrać zadania gruntu i gruntu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="e9d07-134">W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj > nowy element** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="e9d07-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e9d07-135">Wybierz element **pliku konfiguracyjnego NPM,** pozostaw domyślną nazwę, *package.json*i kliknij przycisk **Dodaj.**</span><span class="sxs-lookup"><span data-stu-id="e9d07-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="e9d07-136">W pliku *package.json* wewnątrz `devDependencies` nawiasów klamrowych obiektu wprowadź "grunt".</span><span class="sxs-lookup"><span data-stu-id="e9d07-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="e9d07-137">Wybierz `grunt` z listy Intellisense i naciśnij klawisz Enter.</span><span class="sxs-lookup"><span data-stu-id="e9d07-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="e9d07-138">Visual Studio zacytuje nazwę pakietu chrząkać i dodać dwukropek.</span><span class="sxs-lookup"><span data-stu-id="e9d07-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="e9d07-139">Po prawej stronie dwukropka wybierz najnowszą stabilną wersję pakietu z górnej `Ctrl-Space` części listy Intellisense (naciśnij, jeśli intellisense nie jest wyświetlany).</span><span class="sxs-lookup"><span data-stu-id="e9d07-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![chrząknięcie Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="e9d07-141">NPM używa [semantycznego przechowywania wersji](https://semver.org/) do organizowania zależności.</span><span class="sxs-lookup"><span data-stu-id="e9d07-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="e9d07-142">Przechowywanie wersji semantycznych, znany również jako SemVer, identyfikuje \<pakiety z schematu numeracji głównych>. \<drobnych>. \<> poprawek.</span><span class="sxs-lookup"><span data-stu-id="e9d07-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="e9d07-143">Intellisense upraszcza przechowywanie wersji semantycznych, wyświetlając tylko kilka typowych opcji.</span><span class="sxs-lookup"><span data-stu-id="e9d07-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="e9d07-144">Górny element na liście Intellisense (0.4.5 w powyższym przykładzie) jest uważany za najnowszą stabilną wersję pakietu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="e9d07-145">Symbol cieczy (^) jest zgodny z najnowszą wersją główną, a tylda (~) jest zgodna z najnowszą wersją pomocniczą.</span><span class="sxs-lookup"><span data-stu-id="e9d07-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="e9d07-146">Zobacz [odwołanie do analizatora wersji serwera NPM](https://www.npmjs.com/package/semver) jako przewodnik po pełnej wyrazisty, który zapewnia SemVer.</span><span class="sxs-lookup"><span data-stu-id="e9d07-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="e9d07-147">Dodaj więcej zależności, aby załadować\* pakiety grunt-contrib- do *czyszczenia,* *jshint*, *concat*, *uglify*i *oglądać,* jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="e9d07-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="e9d07-148">Wersje nie muszą odpowiadać przykładowi.</span><span class="sxs-lookup"><span data-stu-id="e9d07-148">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="e9d07-149">Zapisz plik *package.json.*</span><span class="sxs-lookup"><span data-stu-id="e9d07-149">Save the *package.json* file.</span></span>

<span data-ttu-id="e9d07-150">Pakiety dla `devDependencies` każdego elementu zostaną pobrane wraz z wszelkimi plikami, które wymagają każdego pakietu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="e9d07-151">Pliki pakietów można znaleźć w katalogu *node_modules,* włączając przycisk **Pokaż wszystkie pliki** w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="e9d07-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="e9d07-153">W razie potrzeby można ręcznie przywrócić zależności w **Eksploratorze rozwiązań,** klikając prawym przyciskiem myszy `Dependencies\NPM` i wybierając opcję menu **Przywróć pakiety.**</span><span class="sxs-lookup"><span data-stu-id="e9d07-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![przywracanie pakietów](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="e9d07-155">Konfigurowanie gruntu</span><span class="sxs-lookup"><span data-stu-id="e9d07-155">Configuring Grunt</span></span>

<span data-ttu-id="e9d07-156">Grunt jest skonfigurowany przy użyciu manifestu o nazwie *Gruntfile.js,* który definiuje, ładuje i rejestruje zadania, które można uruchomić ręcznie lub skonfigurować do automatycznego uruchamiania na podstawie zdarzeń w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e9d07-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="e9d07-157">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="e9d07-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="e9d07-158">Wybierz szablon elementu **pliku JavaScript,** zmień nazwę na *Gruntfile.js*i kliknij przycisk **Dodaj.**</span><span class="sxs-lookup"><span data-stu-id="e9d07-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="e9d07-159">Dodaj następujący kod do *gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e9d07-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="e9d07-160">Funkcja `initConfig` ustawia opcje dla każdego pakietu, a pozostała część modułu ładuje i rejestruje zadania.</span><span class="sxs-lookup"><span data-stu-id="e9d07-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="e9d07-161">Wewnątrz `initConfig` funkcji dodaj opcje `clean` zadania, jak pokazano w przykładzie *Gruntfile.js* poniżej.</span><span class="sxs-lookup"><span data-stu-id="e9d07-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="e9d07-162">Zadanie `clean` akceptuje tablicę ciągów katalogów.</span><span class="sxs-lookup"><span data-stu-id="e9d07-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="e9d07-163">To zadanie usuwa pliki z *wwwroot/lib* i usuwa cały */temp* katalogu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="e9d07-164">Poniżej `initConfig` funkcji dodaj wywołanie do `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="e9d07-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="e9d07-165">Dzięki temu zadanie będzie można uruchomić z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e9d07-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="e9d07-166">Zapisz *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e9d07-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e9d07-167">Plik powinien wyglądać mniej więcej jak na poniższym zrzucie ekranu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-167">The file should look something like the screenshot below.</span></span>

    ![początkowy plik gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="e9d07-169">Kliknij prawym przyciskiem myszy *gruntfile.js* i wybierz polecenie **Eksploratora uruchamiania zadań** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="e9d07-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="e9d07-170">Zostanie otwarte okno **Eksploratora biegacza zadań.**</span><span class="sxs-lookup"><span data-stu-id="e9d07-170">The **Task Runner Explorer** window will open.</span></span>

    ![Menu eksploratora uruchamiania zadań](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="e9d07-172">Sprawdź, `clean` czy jest wyświetlany w obszarze **Zadania** w **Eksploratorze zadymiań**.</span><span class="sxs-lookup"><span data-stu-id="e9d07-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![Lista zadań eksploratora programu runner zadań](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="e9d07-174">Kliknij prawym przyciskiem myszy czyste zadanie i wybierz polecenie **Uruchom** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="e9d07-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="e9d07-175">W oknie polecenia wyświetlany jest postęp zadania.</span><span class="sxs-lookup"><span data-stu-id="e9d07-175">A command window displays progress of the task.</span></span>

    ![Eksplorator przebiegu zadań uruchamia czyste zadanie](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="e9d07-177">Nie ma jeszcze plików ani katalogów do czyszczenia.</span><span class="sxs-lookup"><span data-stu-id="e9d07-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="e9d07-178">Jeśli chcesz, możesz ręcznie utworzyć je w Eksploratorze rozwiązań, a następnie uruchomić czyste zadanie jako test.</span><span class="sxs-lookup"><span data-stu-id="e9d07-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="e9d07-179">W `initConfig` funkcji dodaj wpis `concat` do korzystania z poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="e9d07-180">Tablica `src` właściwości zawiera listę plików do połączenia w kolejności, w jakiej powinny być łączone.</span><span class="sxs-lookup"><span data-stu-id="e9d07-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="e9d07-181">Właściwość `dest` przypisuje ścieżkę do połączonego pliku, który jest produkowany.</span><span class="sxs-lookup"><span data-stu-id="e9d07-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="e9d07-182">Właściwość `all` w powyższym kodzie jest nazwą obiektu docelowego.</span><span class="sxs-lookup"><span data-stu-id="e9d07-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="e9d07-183">Obiekty docelowe są używane w niektórych zadaniach Grunt, aby umożliwić wiele środowisk kompilacji.</span><span class="sxs-lookup"><span data-stu-id="e9d07-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="e9d07-184">Wbudowane obiekty docelowe można wyświetlić za pomocą funkcji IntelliSense lub przypisać własne.</span><span class="sxs-lookup"><span data-stu-id="e9d07-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="e9d07-185">Dodaj `jshint` zadanie, korzystając z poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="e9d07-186">Narzędzie jshint `code-quality` jest uruchamiane przeciwko każdemu plikowi JavaScript znajdującemu się w katalogu *tymczasowym.*</span><span class="sxs-lookup"><span data-stu-id="e9d07-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="e9d07-187">Opcja "-W069" jest błędem wywoływanym przez jshint, gdy JavaScript używa składni nawiasu do przypisania `Tastes["Sweet"]` właściwości `Tastes.Sweet`zamiast notacji kropkowej, czyli zamiast .</span><span class="sxs-lookup"><span data-stu-id="e9d07-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="e9d07-188">Opcja wyłącza ostrzeżenie, aby umożliwić kontynuowanie pozostałej części procesu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="e9d07-189">Dodaj `uglify` zadanie, korzystając z poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="e9d07-190">Zadanie minifies *plik combined.js* znaleźć w katalogu tymczasowym i tworzy plik wynik w wwwroot/lib po standardowej konwencji nazewnictwa \* \<nazwa\>pliku .min.js\*.</span><span class="sxs-lookup"><span data-stu-id="e9d07-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="e9d07-191">Pod wywołaniem `grunt.loadNpmTasks` tego `grunt-contrib-clean`ładuje , obejmują to samo wywołanie jshint, concat i uglify przy użyciu kodu poniżej.</span><span class="sxs-lookup"><span data-stu-id="e9d07-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="e9d07-192">Zapisz *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e9d07-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e9d07-193">Plik powinien wyglądać mniej więcej jak w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="e9d07-193">The file should look something like the example below.</span></span>

    ![kompletny przykład pliku chrząkania](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="e9d07-195">Należy zauważyć, że lista `clean`Zadania `concat` `jshint` **Eksploratora programu Zadań** zawiera i `uglify` zadania.</span><span class="sxs-lookup"><span data-stu-id="e9d07-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="e9d07-196">Uruchom każde zadanie w kolejności i obserwuj wyniki w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="e9d07-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="e9d07-197">Każde zadanie powinno być uruchamiane bez błędów.</span><span class="sxs-lookup"><span data-stu-id="e9d07-197">Each task should run without errors.</span></span>

    ![Eksplorator przebiegu zadań uruchamia każde zadanie](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="e9d07-199">Zadanie concat tworzy nowy plik *combined.js* i umieszcza go w katalogu tymczasowym.</span><span class="sxs-lookup"><span data-stu-id="e9d07-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="e9d07-200">Zadanie `jshint` po prostu działa i nie generuje danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="e9d07-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="e9d07-201">Zadanie `uglify` tworzy nowy plik *combined.min.js* i umieszcza go w *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="e9d07-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="e9d07-202">Po zakończeniu rozwiązanie powinno wyglądać mniej więcej tak jak na poniższym zrzucie ekranu:</span><span class="sxs-lookup"><span data-stu-id="e9d07-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![eksplorator rozwiązań po wszystkich zadaniach](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="e9d07-204">Aby uzyskać więcej informacji na temat [https://www.npmjs.com/](https://www.npmjs.com/) opcji dla każdego pakietu, odwiedź i wyszukaj nazwę pakietu w polu wyszukiwania na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="e9d07-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="e9d07-205">Na przykład można wyszukać pakiet grunt-contrib-clean, aby uzyskać łącze dokumentacji, które wyjaśnia wszystkie jego parametry.</span><span class="sxs-lookup"><span data-stu-id="e9d07-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="e9d07-206">Wszystko w jednym miejscu</span><span class="sxs-lookup"><span data-stu-id="e9d07-206">All together now</span></span>

<span data-ttu-id="e9d07-207">Użyj Grunt `registerTask()` metody, aby uruchomić serię zadań w określonej sekwencji.</span><span class="sxs-lookup"><span data-stu-id="e9d07-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="e9d07-208">Na przykład, aby uruchomić powyższe kroki w kolejności czyste -> concat -> jshint -> uglify, dodaj poniższy kod do modułu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="e9d07-209">Kod powinien zostać dodany do tego samego poziomu, co wywołania loadNpmTasks(), poza initConfig.</span><span class="sxs-lookup"><span data-stu-id="e9d07-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="e9d07-210">Nowe zadanie pojawi się w Eksploratorze wojedzie zadań w obszarze Zadania aliasu.</span><span class="sxs-lookup"><span data-stu-id="e9d07-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="e9d07-211">Możesz kliknąć prawym przyciskiem myszy i uruchomić go tak samo, jak inne zadania.</span><span class="sxs-lookup"><span data-stu-id="e9d07-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="e9d07-212">Zadanie `all` zostanie `clean`uruchomione `concat` `jshint` , `uglify`i , w kolejności.</span><span class="sxs-lookup"><span data-stu-id="e9d07-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![alias chrząkać zadania](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="e9d07-214">Obserwowanie zmian</span><span class="sxs-lookup"><span data-stu-id="e9d07-214">Watching for changes</span></span>

<span data-ttu-id="e9d07-215">Zadanie `watch` śledzi pliki i katalogi.</span><span class="sxs-lookup"><span data-stu-id="e9d07-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="e9d07-216">Zegarek wyzwala zadania automatycznie, jeśli wykryje zmiany.</span><span class="sxs-lookup"><span data-stu-id="e9d07-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="e9d07-217">Dodaj poniższy kod do initConfig, \*aby obserwować zmiany w plikach .js w katalogu TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e9d07-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="e9d07-218">Jeśli plik JavaScript zostanie `watch` zmieniony, `all` uruchomi zadanie.</span><span class="sxs-lookup"><span data-stu-id="e9d07-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="e9d07-219">Dodaj wywołanie, `loadNpmTasks()` aby `watch` pokazać zadanie w Eksploratorze zadawia.</span><span class="sxs-lookup"><span data-stu-id="e9d07-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="e9d07-220">Kliknij prawym przyciskiem myszy zadanie obserwowanego w Eksploratorze przebiegu zadań i wybierz polecenie Uruchom z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="e9d07-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="e9d07-221">W oknie polecenia z uruchomionym zadaniem zegarka zostanie wyświetlone okno "Oczekiwanie..." Komunikat.</span><span class="sxs-lookup"><span data-stu-id="e9d07-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="e9d07-222">Otwórz jeden z plików TypeScript, dodaj spację, a następnie zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="e9d07-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="e9d07-223">Spowoduje to wyzwolenie zadania zegarka i wyzwoli inne zadania do uruchomienia w kolejności.</span><span class="sxs-lookup"><span data-stu-id="e9d07-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="e9d07-224">Poniższy zrzut ekranu przedstawia przykładowy przebieg.</span><span class="sxs-lookup"><span data-stu-id="e9d07-224">The screenshot below shows a sample run.</span></span>

![uruchamianie zadań wyjściowych](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="e9d07-226">Powiązanie ze zdarzeniami programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9d07-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="e9d07-227">Jeśli nie chcesz ręcznie uruchamiać zadań za każdym razem, gdy pracujesz w programie Visual Studio, należy powiązać zadania z zdarzeniami **Przed kompilacją**, **Po kompilacji**, **Czyszczenie**i **Otwórz projekt.**</span><span class="sxs-lookup"><span data-stu-id="e9d07-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="e9d07-228">Powiąż `watch` tak, aby był uruchamiany przy każdym otwarciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e9d07-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="e9d07-229">W Eksploratorze narzędzia Task Runner kliknij prawym przyciskiem myszy zadanie zegarka i wybierz polecenie Otwarcie**programu** **Powiązań** > z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="e9d07-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![powiązać zadanie z otwarciem projektu](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="e9d07-231">Zwalniaj i ładuj ponownie projekt.</span><span class="sxs-lookup"><span data-stu-id="e9d07-231">Unload and reload the project.</span></span> <span data-ttu-id="e9d07-232">Po ponownym załadowaniu projektu zadanie czujki uruchamia się automatycznie.</span><span class="sxs-lookup"><span data-stu-id="e9d07-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="e9d07-233">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="e9d07-233">Summary</span></span>

<span data-ttu-id="e9d07-234">Grunt jest potężnym biegaczem zadań, który może służyć do automatyzacji większości zadań kompilacji klienta.</span><span class="sxs-lookup"><span data-stu-id="e9d07-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="e9d07-235">Grunt wykorzystuje NPM do dostarczania swoich pakietów i oferuje integrację narzędzi z programem Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e9d07-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="e9d07-236">Eksplorator przebiegu zadań programu Visual Studio wykrywa zmiany w plikach konfiguracyjnych i zapewnia wygodny interfejs do uruchamiania zadań, wyświetlania uruchomionych zadań i powiązania zadań ze zdarzeniami programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e9d07-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
