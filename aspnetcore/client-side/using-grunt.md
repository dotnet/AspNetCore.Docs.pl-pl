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
# <a name="use-grunt-in-aspnet-core"></a>Użyj Grunt w ASP.NET Core

Grunt to biegacz zadań JavaScript, który automatyzuje tworzenie skryptów, kompilację TypeScript, narzędzia "lint" jakości kodu, preprocesy CSS i prawie każdą powtarzalną pracę, która wymaga działania w celu wspierania rozwoju klienta. Grunt jest w pełni obsługiwany w programie Visual Studio.

W tym przykładzie użyto pustego projektu ASP.NET Core jako punktu wyjścia, aby pokazać, jak zautomatyzować proces kompilacji klienta od podstaw.

Gotowy przykład czyści katalog wdrożenia docelowego, łączy pliki JavaScript, sprawdza jakość kodu, kondensuje zawartość pliku JavaScript i wdraża do katalogu głównego aplikacji sieci web. Użyjemy następujących pakietów:

* **grunt**: Pakiet grunt zadanie runner.

* **grunt-contrib-clean**: Wtyczka, która usuwa pliki lub katalogi.

* **grunt-contrib-jshint**: Wtyczka, która recenzuje jakość kodu JavaScript.

* **grunt-contrib-concat**: Wtyczka, która łączy pliki w jeden plik.

* **grunt-contrib-uglify**: Wtyczka, która minifies JavaScript w celu zmniejszenia rozmiaru.

* **grunt-contrib-watch**: Wtyczka, która obserwuje aktywność plików.

## <a name="preparing-the-application"></a>Przygotowanie aplikacji

Aby rozpocząć, skonfiguruj nową pustą aplikację sieci Web i dodaj przykładowe pliki TypeScript. Pliki TypeScript są automatycznie kompilowane do JavaScript przy użyciu domyślnych ustawień programu Visual Studio i będą naszym surowcem do przetwarzania za pomocą Grunt.

1. W programie Visual Studio `ASP.NET Web Application`utwórz nowy program .

2. W oknie dialogowym **Nowy projekt ASP.NET** wybierz szablon ASP.NET **Opróżnij rdzeń** i kliknij przycisk OK.

3. W Eksploratorze rozwiązań przejrzyj strukturę projektu. Folder `\src` zawiera `wwwroot` puste `Dependencies` i węzły.

    ![puste rozwiązanie internetowe](using-grunt/_static/grunt-solution-explorer.png)

4. Dodaj nowy folder `TypeScript` o nazwie do katalogu projektu.

5. Przed dodaniem jakichkolwiek plików upewnij się, że program Visual Studio ma opcję "skompiluj przy zapisywaniu" dla zaznaczonych plików TypeScript. **Project**Przejdź do projektu**Typescript** > **Edytora** > tekstu**opcji** >  **narzędzi:** > 

    ![opcje ustawianie automatycznej kompilacji plików TypeScript](using-grunt/_static/typescript-options.png)

6. Kliknij prawym `TypeScript` przyciskiem myszy katalog i wybierz polecenie **Dodaj > Nowy element** z menu kontekstowego. Wybierz element **pliku JavaScript** i nazwij plik *Tastes.ts* (zwróć uwagę na \*rozszerzenie .ts). Skopiuj wiersz kodu TypeScript poniżej do pliku (po zapisaniu pojawi się nowy plik *Tastes.js* ze źródłem JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Dodaj drugi plik do katalogu **TypeScript** `Food.ts`i nadaj jego nazwę . Skopiuj poniższy kod do pliku.

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

## <a name="configuring-npm"></a>Konfigurowanie serwera NPM

Następnie skonfiguruj NPM, aby pobrać zadania gruntu i gruntu.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj > nowy element** z menu kontekstowego. Wybierz element **pliku konfiguracyjnego NPM,** pozostaw domyślną nazwę, *package.json*i kliknij przycisk **Dodaj.**

2. W pliku *package.json* wewnątrz `devDependencies` nawiasów klamrowych obiektu wprowadź "grunt". Wybierz `grunt` z listy Intellisense i naciśnij klawisz Enter. Visual Studio zacytuje nazwę pakietu chrząkać i dodać dwukropek. Po prawej stronie dwukropka wybierz najnowszą stabilną wersję pakietu z górnej `Ctrl-Space` części listy Intellisense (naciśnij, jeśli intellisense nie jest wyświetlany).

    ![chrząknięcie Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM używa [semantycznego przechowywania wersji](https://semver.org/) do organizowania zależności. Przechowywanie wersji semantycznych, znany również jako SemVer, identyfikuje \<pakiety z schematu numeracji głównych>. \<drobnych>. \<> poprawek. Intellisense upraszcza przechowywanie wersji semantycznych, wyświetlając tylko kilka typowych opcji. Górny element na liście Intellisense (0.4.5 w powyższym przykładzie) jest uważany za najnowszą stabilną wersję pakietu. Symbol cieczy (^) jest zgodny z najnowszą wersją główną, a tylda (~) jest zgodna z najnowszą wersją pomocniczą. Zobacz [odwołanie do analizatora wersji serwera NPM](https://www.npmjs.com/package/semver) jako przewodnik po pełnej wyrazisty, który zapewnia SemVer.

3. Dodaj więcej zależności, aby załadować\* pakiety grunt-contrib- do *czyszczenia,* *jshint*, *concat*, *uglify*i *oglądać,* jak pokazano w poniższym przykładzie. Wersje nie muszą odpowiadać przykładowi.

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

4. Zapisz plik *package.json.*

Pakiety dla `devDependencies` każdego elementu zostaną pobrane wraz z wszelkimi plikami, które wymagają każdego pakietu. Pliki pakietów można znaleźć w katalogu *node_modules,* włączając przycisk **Pokaż wszystkie pliki** w **Eksploratorze rozwiązań**.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> W razie potrzeby można ręcznie przywrócić zależności w **Eksploratorze rozwiązań,** klikając prawym przyciskiem myszy `Dependencies\NPM` i wybierając opcję menu **Przywróć pakiety.**

![przywracanie pakietów](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Konfigurowanie gruntu

Grunt jest skonfigurowany przy użyciu manifestu o nazwie *Gruntfile.js,* który definiuje, ładuje i rejestruje zadania, które można uruchomić ręcznie lub skonfigurować do automatycznego uruchamiania na podstawie zdarzeń w programie Visual Studio.

1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **nowy element**. Wybierz szablon elementu **pliku JavaScript,** zmień nazwę na *Gruntfile.js*i kliknij przycisk **Dodaj.**

1. Dodaj następujący kod do *gruntfile.js*. Funkcja `initConfig` ustawia opcje dla każdego pakietu, a pozostała część modułu ładuje i rejestruje zadania.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Wewnątrz `initConfig` funkcji dodaj opcje `clean` zadania, jak pokazano w przykładzie *Gruntfile.js* poniżej. Zadanie `clean` akceptuje tablicę ciągów katalogów. To zadanie usuwa pliki z *wwwroot/lib* i usuwa cały */temp* katalogu.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Poniżej `initConfig` funkcji dodaj wywołanie do `grunt.loadNpmTasks`. Dzięki temu zadanie będzie można uruchomić z programu Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Zapisz *Gruntfile.js*. Plik powinien wyglądać mniej więcej jak na poniższym zrzucie ekranu.

    ![początkowy plik gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. Kliknij prawym przyciskiem myszy *gruntfile.js* i wybierz polecenie **Eksploratora uruchamiania zadań** z menu kontekstowego. Zostanie otwarte okno **Eksploratora biegacza zadań.**

    ![Menu eksploratora uruchamiania zadań](using-grunt/_static/task-runner-explorer-menu.png)

1. Sprawdź, `clean` czy jest wyświetlany w obszarze **Zadania** w **Eksploratorze zadymiań**.

    ![Lista zadań eksploratora programu runner zadań](using-grunt/_static/task-runner-explorer-tasks.png)

1. Kliknij prawym przyciskiem myszy czyste zadanie i wybierz polecenie **Uruchom** z menu kontekstowego. W oknie polecenia wyświetlany jest postęp zadania.

    ![Eksplorator przebiegu zadań uruchamia czyste zadanie](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Nie ma jeszcze plików ani katalogów do czyszczenia. Jeśli chcesz, możesz ręcznie utworzyć je w Eksploratorze rozwiązań, a następnie uruchomić czyste zadanie jako test.

1. W `initConfig` funkcji dodaj wpis `concat` do korzystania z poniższego kodu.

    Tablica `src` właściwości zawiera listę plików do połączenia w kolejności, w jakiej powinny być łączone. Właściwość `dest` przypisuje ścieżkę do połączonego pliku, który jest produkowany.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > Właściwość `all` w powyższym kodzie jest nazwą obiektu docelowego. Obiekty docelowe są używane w niektórych zadaniach Grunt, aby umożliwić wiele środowisk kompilacji. Wbudowane obiekty docelowe można wyświetlić za pomocą funkcji IntelliSense lub przypisać własne.

1. Dodaj `jshint` zadanie, korzystając z poniższego kodu.

    Narzędzie jshint `code-quality` jest uruchamiane przeciwko każdemu plikowi JavaScript znajdującemu się w katalogu *tymczasowym.*

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Opcja "-W069" jest błędem wywoływanym przez jshint, gdy JavaScript używa składni nawiasu do przypisania `Tastes["Sweet"]` właściwości `Tastes.Sweet`zamiast notacji kropkowej, czyli zamiast . Opcja wyłącza ostrzeżenie, aby umożliwić kontynuowanie pozostałej części procesu.

1. Dodaj `uglify` zadanie, korzystając z poniższego kodu.

    Zadanie minifies *plik combined.js* znaleźć w katalogu tymczasowym i tworzy plik wynik w wwwroot/lib po standardowej konwencji nazewnictwa * \<nazwa\>pliku .min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Pod wywołaniem `grunt.loadNpmTasks` tego `grunt-contrib-clean`ładuje , obejmują to samo wywołanie jshint, concat i uglify przy użyciu kodu poniżej.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Zapisz *Gruntfile.js*. Plik powinien wyglądać mniej więcej jak w poniższym przykładzie.

    ![kompletny przykład pliku chrząkania](using-grunt/_static/gruntfile-js-complete.png)

1. Należy zauważyć, że lista `clean`Zadania `concat` `jshint` **Eksploratora programu Zadań** zawiera i `uglify` zadania. Uruchom każde zadanie w kolejności i obserwuj wyniki w **Eksploratorze rozwiązań**. Każde zadanie powinno być uruchamiane bez błędów.

    ![Eksplorator przebiegu zadań uruchamia każde zadanie](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Zadanie concat tworzy nowy plik *combined.js* i umieszcza go w katalogu tymczasowym. Zadanie `jshint` po prostu działa i nie generuje danych wyjściowych. Zadanie `uglify` tworzy nowy plik *combined.min.js* i umieszcza go w *wwwroot/lib*. Po zakończeniu rozwiązanie powinno wyglądać mniej więcej tak jak na poniższym zrzucie ekranu:

    ![eksplorator rozwiązań po wszystkich zadaniach](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat [https://www.npmjs.com/](https://www.npmjs.com/) opcji dla każdego pakietu, odwiedź i wyszukaj nazwę pakietu w polu wyszukiwania na stronie głównej. Na przykład można wyszukać pakiet grunt-contrib-clean, aby uzyskać łącze dokumentacji, które wyjaśnia wszystkie jego parametry.

### <a name="all-together-now"></a>Wszystko w jednym miejscu

Użyj Grunt `registerTask()` metody, aby uruchomić serię zadań w określonej sekwencji. Na przykład, aby uruchomić powyższe kroki w kolejności czyste -> concat -> jshint -> uglify, dodaj poniższy kod do modułu. Kod powinien zostać dodany do tego samego poziomu, co wywołania loadNpmTasks(), poza initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Nowe zadanie pojawi się w Eksploratorze wojedzie zadań w obszarze Zadania aliasu. Możesz kliknąć prawym przyciskiem myszy i uruchomić go tak samo, jak inne zadania. Zadanie `all` zostanie `clean`uruchomione `concat` `jshint` , `uglify`i , w kolejności.

![alias chrząkać zadania](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Obserwowanie zmian

Zadanie `watch` śledzi pliki i katalogi. Zegarek wyzwala zadania automatycznie, jeśli wykryje zmiany. Dodaj poniższy kod do initConfig, \*aby obserwować zmiany w plikach .js w katalogu TypeScript. Jeśli plik JavaScript zostanie `watch` zmieniony, `all` uruchomi zadanie.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Dodaj wywołanie, `loadNpmTasks()` aby `watch` pokazać zadanie w Eksploratorze zadawia.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Kliknij prawym przyciskiem myszy zadanie obserwowanego w Eksploratorze przebiegu zadań i wybierz polecenie Uruchom z menu kontekstowego. W oknie polecenia z uruchomionym zadaniem zegarka zostanie wyświetlone okno "Oczekiwanie..." Komunikat. Otwórz jeden z plików TypeScript, dodaj spację, a następnie zapisz plik. Spowoduje to wyzwolenie zadania zegarka i wyzwoli inne zadania do uruchomienia w kolejności. Poniższy zrzut ekranu przedstawia przykładowy przebieg.

![uruchamianie zadań wyjściowych](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Powiązanie ze zdarzeniami programu Visual Studio

Jeśli nie chcesz ręcznie uruchamiać zadań za każdym razem, gdy pracujesz w programie Visual Studio, należy powiązać zadania z zdarzeniami **Przed kompilacją**, **Po kompilacji**, **Czyszczenie**i **Otwórz projekt.**

Powiąż `watch` tak, aby był uruchamiany przy każdym otwarciu programu Visual Studio. W Eksploratorze narzędzia Task Runner kliknij prawym przyciskiem myszy zadanie zegarka i wybierz polecenie Otwarcie**programu** **Powiązań** > z menu kontekstowego.

![powiązać zadanie z otwarciem projektu](using-grunt/_static/bindings-project-open.png)

Zwalniaj i ładuj ponownie projekt. Po ponownym załadowaniu projektu zadanie czujki uruchamia się automatycznie.

## <a name="summary"></a>Podsumowanie

Grunt jest potężnym biegaczem zadań, który może służyć do automatyzacji większości zadań kompilacji klienta. Grunt wykorzystuje NPM do dostarczania swoich pakietów i oferuje integrację narzędzi z programem Visual Studio. Eksplorator przebiegu zadań programu Visual Studio wykrywa zmiany w plikach konfiguracyjnych i zapewnia wygodny interfejs do uruchamiania zadań, wyświetlania uruchomionych zadań i powiązania zadań ze zdarzeniami programu Visual Studio.
