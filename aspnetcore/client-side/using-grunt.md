---
title: Używanie grunt w ASP.NET Core
author: rick-anderson
description: Używanie grunt w ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-grunt
ms.openlocfilehash: b51973e82bb1bd382be68a501c40ba613217fb03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773643"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="cd8db-103">Używanie grunt w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd8db-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="cd8db-104">Grunt to moduł uruchamiający zadania języka JavaScript, który automatyzuje skrypty minifikacja, kompilacja języka TypeScript, jakość kodu "lint", a także wszystkie powtarzalne zadania, które wymagają obsługi rozwoju klienta.</span><span class="sxs-lookup"><span data-stu-id="cd8db-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="cd8db-105">Grunt jest w pełni obsługiwany w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd8db-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="cd8db-106">Ten przykład używa pustego projektu ASP.NET Core jako punktu początkowego, aby pokazać, jak zautomatyzować proces kompilacji klienta od podstaw.</span><span class="sxs-lookup"><span data-stu-id="cd8db-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="cd8db-107">Zakończono przykład czyści docelowy katalog wdrożenia, łączy pliki JavaScript, sprawdza jakość kodu, skrapla zawartość pliku JavaScript i wdraża je w katalogu głównym aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="cd8db-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="cd8db-108">Będziemy używać następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="cd8db-108">We will use the following packages:</span></span>

* <span data-ttu-id="cd8db-109">**grunt**: pakiet Runner zadania grunt.</span><span class="sxs-lookup"><span data-stu-id="cd8db-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="cd8db-110">**grunt-contrib-Clean**: wtyczka, która usuwa pliki lub katalogi.</span><span class="sxs-lookup"><span data-stu-id="cd8db-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="cd8db-111">**grunt-contrib-jshint**: wtyczka, która przegląda jakość kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cd8db-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="cd8db-112">**grunt-contrib-concat**: wtyczka, która przyłącza pliki do pojedynczego pliku.</span><span class="sxs-lookup"><span data-stu-id="cd8db-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="cd8db-113">**grunt-contrib-uglify**: wtyczka, która minifies JavaScript, aby zmniejszyć rozmiar.</span><span class="sxs-lookup"><span data-stu-id="cd8db-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="cd8db-114">**grunt-contrib-Watch**: wtyczka, która obserwuje aktywność pliku.</span><span class="sxs-lookup"><span data-stu-id="cd8db-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="cd8db-115">Przygotowywanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="cd8db-115">Preparing the application</span></span>

<span data-ttu-id="cd8db-116">Aby rozpocząć, skonfiguruj nową pustą aplikację sieci Web i Dodaj przykładowe pliki języka TypeScript.</span><span class="sxs-lookup"><span data-stu-id="cd8db-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="cd8db-117">Pliki TypeScript są automatycznie kompilowane do języka JavaScript przy użyciu domyślnych ustawień programu Visual Studio i będą nasze surowy materiał do przetwarzania za pomocą grunt.</span><span class="sxs-lookup"><span data-stu-id="cd8db-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="cd8db-118">W programie Visual Studio Utwórz nowy `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="cd8db-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="cd8db-119">W oknie dialogowym **Nowy projekt ASP.NET** wybierz ASP.NET Core **pusty** szablon i kliknij przycisk OK.</span><span class="sxs-lookup"><span data-stu-id="cd8db-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="cd8db-120">W Eksplorator rozwiązań Przejrzyj strukturę projektu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="cd8db-121">`\src` Folder zawiera puste `wwwroot` i `Dependencies` węzły.</span><span class="sxs-lookup"><span data-stu-id="cd8db-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![puste rozwiązanie sieci Web](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="cd8db-123">Dodaj nowy folder o nazwie `TypeScript` do katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="cd8db-124">Przed dodaniem jakichkolwiek plików upewnij się, że program Visual Studio ma zaznaczoną opcję "Kompiluj przy zapisywaniu" dla plików TypeScript.</span><span class="sxs-lookup"><span data-stu-id="cd8db-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="cd8db-125">Przejdź do **Tools** > **opcji** > **narzędzia Edytor** > tekstu język**TypeScript** > **projekt**:</span><span class="sxs-lookup"><span data-stu-id="cd8db-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![Opcje ustawiania opcji autokompilowania plików TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="cd8db-127">Kliknij prawym przyciskiem `TypeScript` myszy katalog i wybierz polecenie **Dodaj > nowy element** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="cd8db-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="cd8db-128">Wybierz element **pliku JavaScript** i Nazwij plik *smaku. TS* (zanotuj rozszerzenie \*. TS).</span><span class="sxs-lookup"><span data-stu-id="cd8db-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="cd8db-129">Skopiuj wiersz kodu języka TypeScript poniżej do pliku (podczas zapisywania zostanie wyświetlony nowy plik *smak. js* ze źródłem JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cd8db-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="cd8db-130">Dodaj drugi plik do katalogu **TypeScript** i nadaj mu `Food.ts`nazwę.</span><span class="sxs-lookup"><span data-stu-id="cd8db-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="cd8db-131">Skopiuj poniższy kod do pliku.</span><span class="sxs-lookup"><span data-stu-id="cd8db-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="cd8db-132">Konfigurowanie NPM</span><span class="sxs-lookup"><span data-stu-id="cd8db-132">Configuring NPM</span></span>

<span data-ttu-id="cd8db-133">Następnie skonfiguruj NPM do pobierania grunt i grunt-Tasks.</span><span class="sxs-lookup"><span data-stu-id="cd8db-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="cd8db-134">W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **dodaj > nowy element** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="cd8db-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="cd8db-135">Wybierz element **pliku konfiguracji npm** , pozostaw nazwę domyślną, *Package. JSON*, a następnie kliknij przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="cd8db-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="cd8db-136">W `devDependencies` pliku *Package. JSON* w nawiasach klamrowych wpisz "Grunt".</span><span class="sxs-lookup"><span data-stu-id="cd8db-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="cd8db-137">Wybierz `grunt` z listy IntelliSense i naciśnij klawisz ENTER.</span><span class="sxs-lookup"><span data-stu-id="cd8db-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="cd8db-138">Program Visual Studio zwróci nazwę pakietu grunt i doda dwukropek.</span><span class="sxs-lookup"><span data-stu-id="cd8db-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="cd8db-139">Z prawej strony dwukropka wybierz najnowszą stabilną wersję pakietu z góry listy IntelliSense (kliknij `Ctrl-Space` , jeśli IntelliSense nie pojawia się).</span><span class="sxs-lookup"><span data-stu-id="cd8db-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="cd8db-141">NPM używa [semantycznej wersji](https://semver.org/) do organizowania zależności.</span><span class="sxs-lookup"><span data-stu-id="cd8db-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="cd8db-142">Wersje semantyczne, znane również jako SemVer, identyfikują pakiety z schematem \<numeracji głównym>. \<> pomocnicze. \<poprawka>.</span><span class="sxs-lookup"><span data-stu-id="cd8db-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="cd8db-143">Technologia IntelliSense upraszcza obsługę wersji semantycznych, pokazując tylko kilka typowych opcji.</span><span class="sxs-lookup"><span data-stu-id="cd8db-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="cd8db-144">Górny element na liście IntelliSense (0.4.5 w powyższym przykładzie) jest traktowany jako Najnowsza stabilna wersja pakietu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="cd8db-145">Symbol karetki (^) jest zgodny z najnowszą wersją główną, a Tylda (~) jest zgodna z najnowszą wersją pomocniczą.</span><span class="sxs-lookup"><span data-stu-id="cd8db-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="cd8db-146">Zapoznaj się z informacjami o [analizatorze analizatora wersji npm semver](https://www.npmjs.com/package/semver) jako wskazówką dla expressivity semver.</span><span class="sxs-lookup"><span data-stu-id="cd8db-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="cd8db-147">Dodaj więcej zależności, aby załadować grunt-contrib\* -packages for *Clean*, *jshint*, *concat*, *uglify*i *Watch* , jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="cd8db-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="cd8db-148">Wersje nie muszą być zgodne z przykładem.</span><span class="sxs-lookup"><span data-stu-id="cd8db-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="cd8db-149">Zapisz plik *Package. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cd8db-149">Save the *package.json* file.</span></span>

<span data-ttu-id="cd8db-150">Pakiety dla każdego `devDependencies` elementu zostaną pobrane wraz z wszystkimi plikami wymaganymi przez każdy pakiet.</span><span class="sxs-lookup"><span data-stu-id="cd8db-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="cd8db-151">Pliki pakietów można znaleźć w katalogu *node_modules* , włączając przycisk **Pokaż wszystkie pliki** w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="cd8db-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="cd8db-153">Jeśli zachodzi taka potrzeba, można ręcznie przywrócić zależności w **Eksplorator rozwiązań** przez kliknięcie prawym przyciskiem `Dependencies\NPM` myszy i wybranie opcji menu **Przywróć pakiety** .</span><span class="sxs-lookup"><span data-stu-id="cd8db-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![Przywróć pakiety](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="cd8db-155">Konfigurowanie grunt</span><span class="sxs-lookup"><span data-stu-id="cd8db-155">Configuring Grunt</span></span>

<span data-ttu-id="cd8db-156">Grunt jest skonfigurowany przy użyciu manifestu o nazwie *Gruntfile. js* , który definiuje, ładuje i rejestruje zadania, które można uruchomić ręcznie lub skonfigurować do automatycznego uruchamiania na podstawie zdarzeń w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd8db-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="cd8db-157">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="cd8db-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="cd8db-158">Wybierz szablon element **pliku JavaScript** , Zmień nazwę na *Gruntfile. js*, a następnie kliknij przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="cd8db-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="cd8db-159">Dodaj następujący kod do *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="cd8db-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="cd8db-160">`initConfig` Funkcja ustawia opcje dla każdego pakietu, a pozostała część modułu ładuje i rejestruje zadania.</span><span class="sxs-lookup"><span data-stu-id="cd8db-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="cd8db-161">Wewnątrz `initConfig` funkcji, należy dodać opcje dla `clean` zadania, jak pokazano w przykładzie *Gruntfile. js* poniżej.</span><span class="sxs-lookup"><span data-stu-id="cd8db-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="cd8db-162">`clean` Zadanie akceptuje tablicę ciągów katalogów.</span><span class="sxs-lookup"><span data-stu-id="cd8db-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="cd8db-163">To zadanie usuwa pliki z katalogu *wwwroot/lib* i usuwa cały katalog */temp* .</span><span class="sxs-lookup"><span data-stu-id="cd8db-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="cd8db-164">Poniżej `initConfig` funkcji Dodaj wywołanie do `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="cd8db-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="cd8db-165">Spowoduje to możliwy do uruchomienia zadania z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd8db-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="cd8db-166">Zapisz *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="cd8db-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="cd8db-167">Plik powinien wyglądać podobnie do poniższego zrzutu ekranu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-167">The file should look something like the screenshot below.</span></span>

    ![początkowy gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="cd8db-169">Kliknij prawym przyciskiem myszy pozycję *Gruntfile. js* i wybierz pozycję **Eksplorator modułu uruchamiającego zadania** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="cd8db-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="cd8db-170">Zostanie otwarte okno **Eksplorator Runner zadań** .</span><span class="sxs-lookup"><span data-stu-id="cd8db-170">The **Task Runner Explorer** window will open.</span></span>

    ![menu Eksploratora modułu uruchamiającego zadania](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="cd8db-172">Sprawdź, `clean` czy w obszarze **zadania** w **Eksploratorze modułu uruchamiającego zadania**jest wyświetlana wartość.</span><span class="sxs-lookup"><span data-stu-id="cd8db-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![Lista zadań Eksploratora modułu uruchamiającego zadania](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="cd8db-174">Kliknij prawym przyciskiem myszy zadanie czyste i wybierz polecenie **Uruchom** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="cd8db-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="cd8db-175">W oknie polecenia zostanie wyświetlony postęp zadania.</span><span class="sxs-lookup"><span data-stu-id="cd8db-175">A command window displays progress of the task.</span></span>

    ![Eksplorator modułu uruchamiającego zadania — uruchamianie czystego zadania](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="cd8db-177">Nie ma jeszcze plików lub katalogów do czyszczenia.</span><span class="sxs-lookup"><span data-stu-id="cd8db-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="cd8db-178">Jeśli chcesz, możesz je ręcznie utworzyć w Eksplorator rozwiązań a następnie uruchomić czyste zadanie jako test.</span><span class="sxs-lookup"><span data-stu-id="cd8db-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="cd8db-179">W `initConfig` funkcji Dodaj wpis do `concat` użycia poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="cd8db-180">Tablica `src` właściwości zawiera listę plików do połączenia w kolejności, w jakiej powinny być połączone.</span><span class="sxs-lookup"><span data-stu-id="cd8db-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="cd8db-181">`dest` Właściwość przypisuje ścieżkę do połączonego pliku, który został utworzony.</span><span class="sxs-lookup"><span data-stu-id="cd8db-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="cd8db-182">`all` Właściwość w powyższym kodzie jest nazwą obiektu docelowego.</span><span class="sxs-lookup"><span data-stu-id="cd8db-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="cd8db-183">Elementy docelowe są używane w niektórych zadaniach grunt, aby umożliwić wiele środowisk kompilacji.</span><span class="sxs-lookup"><span data-stu-id="cd8db-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="cd8db-184">Możesz wyświetlić wbudowane elementy docelowe przy użyciu funkcji IntelliSense lub przypisać własne.</span><span class="sxs-lookup"><span data-stu-id="cd8db-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="cd8db-185">Dodaj `jshint` zadanie przy użyciu poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="cd8db-186">Narzędzie jshint `code-quality` jest uruchamiane dla każdego pliku JavaScript znajdującego się w katalogu *temp* .</span><span class="sxs-lookup"><span data-stu-id="cd8db-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="cd8db-187">Opcja "-W069" jest błędem wytwarzanym przez jshint, gdy język JavaScript używa składni nawiasu do przypisywania właściwości zamiast notacji `Tastes["Sweet"]` kropkowej `Tastes.Sweet`, czyli zamiast.</span><span class="sxs-lookup"><span data-stu-id="cd8db-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="cd8db-188">Opcja wyłącza ostrzeżenie, aby umożliwić kontynuowanie pozostałej części procesu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="cd8db-189">Dodaj `uglify` zadanie przy użyciu poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="cd8db-190">Zadanie minifies połączony plik *js* znajdujący się w katalogu tymczasowym i tworzy plik wynikowy w pliku wwwroot/lib, postępując zgodnie ze standardową \* \<nazwą\>pliku konwencji nazewnictwa. min. js\*.</span><span class="sxs-lookup"><span data-stu-id="cd8db-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="cd8db-191">W ramach wywołania do `grunt.loadNpmTasks` tych obciążeń `grunt-contrib-clean`należy uwzględnić to samo wywołanie dla jshint, concat i uglify przy użyciu poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="cd8db-192">Zapisz *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="cd8db-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="cd8db-193">Plik powinien wyglądać podobnie do poniższego przykładu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-193">The file should look something like the example below.</span></span>

    ![Zakończono przykład pliku grunt](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="cd8db-195">Zwróć uwagę, że lista zadań **Eksploratora modułu uruchamiającego zadania** zawiera `clean`zadania, `concat` `jshint` i `uglify` .</span><span class="sxs-lookup"><span data-stu-id="cd8db-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="cd8db-196">Uruchom każde zadanie w kolejności i obserwuj wyniki w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="cd8db-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="cd8db-197">Każde zadanie powinno być uruchamiane bez błędów.</span><span class="sxs-lookup"><span data-stu-id="cd8db-197">Each task should run without errors.</span></span>

    ![Eksplorator modułu uruchamiającego zadania uruchamia każde zadanie](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="cd8db-199">Zadanie concat tworzy nowy połączony plik *. js* i umieszcza go w katalogu Temp.</span><span class="sxs-lookup"><span data-stu-id="cd8db-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="cd8db-200">`jshint` Zadanie jest uruchamiane po prostu i nie tworzy danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="cd8db-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="cd8db-201">`uglify` Zadanie tworzy nowy połączony plik *. min. js* i umieszcza go w pliku *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="cd8db-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="cd8db-202">Po zakończeniu rozwiązanie powinno wyglądać podobnie do poniższego zrzutu ekranu:</span><span class="sxs-lookup"><span data-stu-id="cd8db-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![Eksplorator rozwiązań po wszystkich zadaniach](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="cd8db-204">Aby uzyskać więcej informacji na temat opcji dla każdego pakietu, [https://www.npmjs.com/](https://www.npmjs.com/) należy odwiedzić stronę i przeszukać ją w polu wyszukiwania na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="cd8db-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="cd8db-205">Na przykład można wyszukać pakiet grunt-contrib-Clean w celu uzyskania linku do dokumentacji, który objaśnia wszystkie jego parametry.</span><span class="sxs-lookup"><span data-stu-id="cd8db-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="cd8db-206">Wszystko w jednym miejscu</span><span class="sxs-lookup"><span data-stu-id="cd8db-206">All together now</span></span>

<span data-ttu-id="cd8db-207">Użyj metody grunt `registerTask()` , aby uruchomić serię zadań w określonej kolejności.</span><span class="sxs-lookup"><span data-stu-id="cd8db-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="cd8db-208">Na przykład aby uruchomić przykładowe kroki opisane powyżej w kolejności > concat > jshint-> uglify, Dodaj poniższy kod do modułu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="cd8db-209">Kod powinien zostać dodany do tego samego poziomu, co wywołania loadNpmTasks (), poza initConfig.</span><span class="sxs-lookup"><span data-stu-id="cd8db-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="cd8db-210">Nowe zadanie zostanie wyświetlone w Eksploratorze modułu uruchamiającego zadania w obszarze zadania aliasu.</span><span class="sxs-lookup"><span data-stu-id="cd8db-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="cd8db-211">Możesz kliknąć prawym przyciskiem myszy i uruchomić go tak samo jak w przypadku innych zadań.</span><span class="sxs-lookup"><span data-stu-id="cd8db-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="cd8db-212">`all` `clean`Zadanie zostanie uruchomione `concat`, `jshint` i `uglify`, w określonej kolejności.</span><span class="sxs-lookup"><span data-stu-id="cd8db-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![grunt zadania aliasu](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="cd8db-214">Obserwowane zmiany</span><span class="sxs-lookup"><span data-stu-id="cd8db-214">Watching for changes</span></span>

<span data-ttu-id="cd8db-215">`watch` Zadanie zachowuje oczy dotyczące plików i katalogów.</span><span class="sxs-lookup"><span data-stu-id="cd8db-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="cd8db-216">Czujka wyzwala zadania automatycznie w przypadku wykrycia zmian.</span><span class="sxs-lookup"><span data-stu-id="cd8db-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="cd8db-217">Dodaj poniższy kod do initConfig, aby obejrzeć zmiany \*plików js w katalogu TypeScript.</span><span class="sxs-lookup"><span data-stu-id="cd8db-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="cd8db-218">Jeśli plik JavaScript zostanie zmieniony, `watch` program uruchomi `all` zadanie.</span><span class="sxs-lookup"><span data-stu-id="cd8db-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="cd8db-219">Dodaj wywołanie do, `loadNpmTasks()` `watch` aby wyświetlić zadanie w Eksploratorze modułu uruchamiającego zadania.</span><span class="sxs-lookup"><span data-stu-id="cd8db-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="cd8db-220">Kliknij prawym przyciskiem myszy zadanie Obejrzyj w Eksploratorze modułu uruchamiającego zadania i wybierz polecenie Uruchom z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="cd8db-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="cd8db-221">W oknie wiersza polecenia, w którym jest uruchomione zadanie Obserwuj, zostanie wyświetlony element "oczekiwanie..." Komunikat.</span><span class="sxs-lookup"><span data-stu-id="cd8db-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="cd8db-222">Otwórz jeden z plików TypeScript, Dodaj spację, a następnie Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="cd8db-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="cd8db-223">Spowoduje to wyzwolenie zadania czujki i wyzwolenie innych zadań do uruchomienia w pożądanej kolejności.</span><span class="sxs-lookup"><span data-stu-id="cd8db-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="cd8db-224">Poniższy zrzut ekranu przedstawia przykład uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="cd8db-224">The screenshot below shows a sample run.</span></span>

![uruchomione zadania wyjściowe](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="cd8db-226">Powiązanie ze zdarzeniami programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd8db-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="cd8db-227">Jeśli nie chcesz ręcznie uruchamiać zadań za każdym razem, gdy Pracujesz w programie Visual Studio, powiąż zadania z **przed kompilacją**, **po kompilacji**, **czyszczeniu**i otwartych zdarzeniach **projektu** .</span><span class="sxs-lookup"><span data-stu-id="cd8db-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="cd8db-228">Powiąż `watch` , aby było uruchamiane za każdym razem, gdy zostanie otwarty program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd8db-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="cd8db-229">W Eksploratorze modułu uruchamiającego zadania kliknij prawym przyciskiem myszy zadanie Obserwuj i wybierz polecenie **powiązania** > **projektu otwarte** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="cd8db-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![Powiąż zadanie z otwierającym projektu](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="cd8db-231">Zwolnij i Załaduj ponownie projekt.</span><span class="sxs-lookup"><span data-stu-id="cd8db-231">Unload and reload the project.</span></span> <span data-ttu-id="cd8db-232">Po ponownym załadowaniu projektu zadanie czujki rozpocznie się automatycznie.</span><span class="sxs-lookup"><span data-stu-id="cd8db-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="cd8db-233">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="cd8db-233">Summary</span></span>

<span data-ttu-id="cd8db-234">Grunt to zaawansowany moduł uruchamiający zadania, którego można użyć do zautomatyzowania większości zadań kompilacji klienta.</span><span class="sxs-lookup"><span data-stu-id="cd8db-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="cd8db-235">Grunt wykorzystuje NPM do dostarczania pakietów i narzędzi integracji z programem Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd8db-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="cd8db-236">Eksplorator modułu uruchamiającego zadania programu Visual Studio wykrywa zmiany w plikach konfiguracji i udostępnia wygodny interfejs do uruchamiania zadań, wyświetlania uruchomionych zadań i powiązania zadań z zdarzeniami programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd8db-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
