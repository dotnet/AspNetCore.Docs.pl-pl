---
title: Dodawanie kontrolera do aplikacji core mvc ASP.NET
author: rick-anderson
description: Dowiedz się, jak dodać kontroler do prostej aplikacji Core MVC ASP.NET.
ms.author: riande
ms.date: 08/05/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: fb670902b0dafa7dce2b3372e550095387844936
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666993"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a>Dodawanie kontrolera do aplikacji core mvc ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Wzorzec architektoniczny kontrolera widoku modelu (MVC) oddziela aplikację na trzy główne składniki: **M**odel, **V**iew i **C**ontroller. Wzorzec MVC ułatwia tworzenie aplikacji, które są bardziej sprawdzalne i łatwiejsze do aktualizacji niż tradycyjne aplikacje monolityczne. Aplikacje oparte na MVC zawierają:

* **M**odels: Klasy, które reprezentują dane aplikacji. Klasy modelu używają logiki sprawdzania poprawności, aby wymusić reguły biznesowe dla tych danych. Zazwyczaj obiekty modelu pobierają i przechowują stan modelu w bazie danych. W tym samouczku `Movie` model pobiera dane filmowe z bazy danych, udostępnia je do widoku lub aktualizuje go. Zaktualizowane dane są zapisywane w bazie danych.

* **V**iews: Widoki są składniki, które wyświetlają interfejs użytkownika aplikacji (UI). Ogólnie rzecz biorąc ten interfejs użytkownika wyświetla dane modelu.

* **C**ontrollers: klasy, które obsługują żądania przeglądarki. Pobierają dane modelu i szablony widoku wywołań, które zwracają odpowiedź. W aplikacji MVC w widoku są wyświetlane tylko informacje; kontroler obsługuje i reaguje na dane wejściowe i interakcję użytkownika. Na przykład kontroler obsługuje dane trasy i wartości ciągów zapytania i przekazuje te wartości do modelu. Model może używać tych wartości do wykonywania zapytań do bazy danych. Na przykład `https://localhost:5001/Home/Privacy` ma dane `Home` trasy (kontroler) i `Privacy` (metoda akcji, aby wywołać na kontrolerze domowym). `https://localhost:5001/Movies/Edit/5`to żądanie edycji filmu z identyfikatorem ID=5 przy użyciu kontrolera filmów. Dane trasy jest wyjaśnione w dalszej części samouczka.

Wzorzec MVC pomaga tworzyć aplikacje, które oddzielają różne aspekty aplikacji (logika wprowadzania, logika biznesowa i logika interfejsu użytkownika), zapewniając jednocześnie luźne sprzężenie między tymi elementami. Wzorzec określa, gdzie każdy rodzaj logiki powinny znajdować się w aplikacji. Logika interfejsu użytkownika należy do widoku. Logika wejściowa należy do kontrolera. Logika biznesowa należy do modelu. Ta separacja ułatwia zarządzanie złożonością podczas tworzenia aplikacji, ponieważ umożliwia pracę nad jednym aspektem implementacji w czasie bez wpływu na kod innego. Na przykład można pracować nad kodem widoku bez w zależności od kodu logiki biznesowej.

Omówimy te pojęcia w tej serii samouczków i pokażemy, jak ich używać do tworzenia aplikacji filmowej. Projekt MVC zawiera foldery *dla kontrolerów* i *widoków*.

## <a name="add-a-controller"></a>Dodawanie kontrolera

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **polecenie Kontrolery > menu**
  ![kontekstowe Dodaj kontroler >](adding-controller/_static/add_controller.png)

* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC — puste**

  ![Dodaj kontroler MVC i nadaj mu nazwę](adding-controller/_static/ac.png)

* W **oknie dialogowym Dodawanie pustego kontrolera MVC**wprowadź **helloworldcontroller** i wybierz pozycję **ADD**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Wybierz ikonę **EXPLORER,** a następnie kliknij (kliknij prawym przyciskiem myszy) **kontrolery > nowy plik** i nadaj nowy plik *HelloWorldController.cs*.

  ![Menu kontekstowe](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **kontrolery > dodaj > nowy plik**.
![Menu kontekstowe](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Wybierz **ASP.NET klasę kontrolera Core** i **MVC**.

Nazwij kontroler **HelloWorldController**.

![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Zastąp zawartość *controllers/HelloWorldController.cs* na następujące:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Każda `public` metoda w kontrolerze jest wywoływana jako punkt końcowy HTTP. W powyższym przykładzie obie metody zwracają ciąg. Zanotuj komentarze poprzedzające każdą metodę.

Punkt końcowy HTTP jest docelowym adresem URL w `https://localhost:5001/HelloWorld`aplikacji internetowej, takim `HTTPS`jak , i łączy używany protokół: , `localhost:5001` lokalizację sieciową `HelloWorld`serwera sieci web (w tym portu TCP): i docelowy identyfikator URI .

Pierwszy komentarz stwierdza, że jest to metoda [HTTP GET,](https://www.w3schools.com/tags/ref_httpmethods.asp) która jest wywoływana przez dołączenie `/HelloWorld/` do podstawowego adresu URL. Drugi komentarz określa metodę [HTTP GET,](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) która jest wywoływana przez dołączenie `/HelloWorld/Welcome/` do adresu URL. W dalszej części samouczka aparat szkieletu `HTTP POST` jest używany do generowania metod, które aktualizują dane.

Uruchom aplikację w trybie bez debugowania i dołącz "HelloWorld" do ścieżki na pasku adresu. Metoda `Index` zwraca ciąg.

![Okno przeglądarki z odpowiedzią aplikacji Jest to moja domyślna akcja](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC wywołuje klasy kontrolera (i metody akcji w nich) w zależności od przychodzącego adresu URL. Domyślna [logika routingu adresów URL](xref:mvc/controllers/routing) używana przez MVC używa takiego formatu, aby określić, jaki kod ma wywołać:

`/[Controller]/[ActionName]/[Parameters]`

Format routingu jest `Configure` ustawiony w metodzie w *pliku Startup.cs.*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Gdy przeglądasz aplikację i nie dostarczasz żadnych segmentów adresów URL, domyślnie kontroler "Home" i metoda "Indeks" określona w wierszu szablonu wyróżnionym powyżej.

Pierwszy segment adresu URL określa klasę kontrolera do uruchomienia. Tak `localhost:{PORT}/HelloWorld` mapuje do **HelloWorld**Controller klasy. Druga część segmentu ADRESU URL określa metodę akcji w klasie. Tak `localhost:{PORT}/HelloWorld/Index` więc `Index` spowodowałoby, `HelloWorldController` że metoda klasy do uruchomienia. Należy zauważyć, że trzeba `localhost:{PORT}/HelloWorld` było `Index` tylko przeglądać i metoda została wywołana domyślnie. To dlatego, `Index` że jest domyślną metodą, która zostanie wywołana na kontrolerze, jeśli nazwa metody nie jest jawnie określony. Trzecia część segmentu adresu `id`URL ( ) dotyczy danych trasy. Dane trasy jest wyjaśnione w dalszej części samouczka.

Przejdź do `https://localhost:{PORT}/HelloWorld/Welcome`. Metoda `Welcome` uruchamia się i `This is the Welcome action method...`zwraca ciąg . Dla tego adresu URL `HelloWorld` `Welcome` kontroler jest i jest metodą akcji. Część adresu URL `[Parameters]` nie została jeszcze użyta.

![Okno przeglądarki z odpowiedzią aplikacji Jest to metoda akcji Powitalna](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Zmodyfikuj kod, aby przekazać niektóre informacje o parametrach z adresu URL do kontrolera. Na przykład `/HelloWorld/Welcome?name=Rick&numtimes=4`. Zmień `Welcome` metodę, aby uwzględnić dwa parametry, jak pokazano w poniższym kodzie.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Powyższy kod ma następujące działanie:

* Używa funkcji parametru opcjonalnego języka C#, aby wskazać, że `numTimes` parametr jest domyślnie 1, jeśli nie jest przekazywana żadna wartość dla tego parametru. <!-- remove for simplified -->
* Służy `HtmlEncoder.Default.Encode` do ochrony aplikacji przed złośliwym wprowadzaniem danych (a mianowicie JavaScript).
* Używa [interpolowanych ciągów](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) w `$"Hello {name}, NumTimes is: {numTimes}"`pliku . <!-- remove for simplified -->

Uruchom aplikację i przejdź do:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Zamień `{PORT}` numer portu). Możesz wypróbować różne `name` wartości `numtimes` dla i w adresie URL. System [wiązania modelu](xref:mvc/models/model-binding) MVC automatycznie mapuje nazwane parametry z ciągu zapytania na pasku adresu do parametrów w metodzie. Zobacz [powiązanie modelu, aby](xref:mvc/models/model-binding) uzyskać więcej informacji.

![Okno przeglądarki pokazujące odpowiedź aplikacji Hello Rick,\: NumTimes jest 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na powyższym obrazie segment`Parameters`adresu URL ( `name` ) `numTimes` nie jest używany, parametry i są przekazywane w [ciągu zapytania](https://wikipedia.org/wiki/Query_string). `?` (Znak zapytania) w powyższym adresie URL jest separatorem, a ciąg zapytania następuje. Znak `&` oddziela pary wartości pola.

Zastąp metodę `Welcome` poniższym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uruchom aplikację i wprowadź następujący adres URL:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tym razem trzeci segment adresu URL `id`odpowiadał parametrowi trasy . Metoda `Welcome` zawiera parametr, `id` który pasował do `MapControllerRoute` szablonu adresu URL w metodzie. Trailing `?` (in) `id?`wskazuje, `id` że parametr jest opcjonalny.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

W tych przykładach kontroler robi "VC" część MVC - czyli **V**iew i **C**ontroller pracy. Kontroler zwraca kod HTML bezpośrednio. Ogólnie nie chcesz kontrolerów zwracanych HTML bezpośrednio, ponieważ staje się to bardzo uciążliwe dla kodu i utrzymania. Zamiast tego zazwyczaj używasz oddzielnego pliku szablonu widoku Razor do wygenerowania odpowiedzi HTML. Można to zrobić w następnym samouczku.

> [!div class="step-by-step"]
> [Poprzedni](start-mvc.md)
> [następny](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Wzorzec architektoniczny kontrolera widoku modelu (MVC) oddziela aplikację na trzy główne składniki: **M**odel, **V**iew i **C**ontroller. Wzorzec MVC ułatwia tworzenie aplikacji, które są bardziej sprawdzalne i łatwiejsze do aktualizacji niż tradycyjne aplikacje monolityczne. Aplikacje oparte na MVC zawierają:

* **M**odels: Klasy, które reprezentują dane aplikacji. Klasy modelu używają logiki sprawdzania poprawności, aby wymusić reguły biznesowe dla tych danych. Zazwyczaj obiekty modelu pobierają i przechowują stan modelu w bazie danych. W tym samouczku `Movie` model pobiera dane filmowe z bazy danych, udostępnia je do widoku lub aktualizuje go. Zaktualizowane dane są zapisywane w bazie danych.

* **V**iews: Widoki są składniki, które wyświetlają interfejs użytkownika aplikacji (UI). Ogólnie rzecz biorąc ten interfejs użytkownika wyświetla dane modelu.

* **C**ontrollers: klasy, które obsługują żądania przeglądarki. Pobierają dane modelu i szablony widoku wywołań, które zwracają odpowiedź. W aplikacji MVC w widoku są wyświetlane tylko informacje; kontroler obsługuje i reaguje na dane wejściowe i interakcję użytkownika. Na przykład kontroler obsługuje dane trasy i wartości ciągów zapytania i przekazuje te wartości do modelu. Model może używać tych wartości do wykonywania zapytań do bazy danych. Na przykład `https://localhost:5001/Home/About` ma dane `Home` trasy (kontroler) i `About` (metoda akcji, aby wywołać na kontrolerze domowym). `https://localhost:5001/Movies/Edit/5`to żądanie edycji filmu z identyfikatorem ID=5 przy użyciu kontrolera filmów. Dane trasy jest wyjaśnione w dalszej części samouczka.

Wzorzec MVC pomaga tworzyć aplikacje, które oddzielają różne aspekty aplikacji (logika wprowadzania, logika biznesowa i logika interfejsu użytkownika), zapewniając jednocześnie luźne sprzężenie między tymi elementami. Wzorzec określa, gdzie każdy rodzaj logiki powinny znajdować się w aplikacji. Logika interfejsu użytkownika należy do widoku. Logika wejściowa należy do kontrolera. Logika biznesowa należy do modelu. Ta separacja ułatwia zarządzanie złożonością podczas tworzenia aplikacji, ponieważ umożliwia pracę nad jednym aspektem implementacji w czasie bez wpływu na kod innego. Na przykład można pracować nad kodem widoku bez w zależności od kodu logiki biznesowej.

Omówimy te pojęcia w tej serii samouczków i pokażemy, jak ich używać do tworzenia aplikacji filmowej. Projekt MVC zawiera foldery *dla kontrolerów* i *widoków*.

## <a name="add-a-controller"></a>Dodawanie kontrolera

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **polecenie Kontrolery > menu**
  ![kontekstowe Dodaj kontroler >](adding-controller/_static/add_controller.png)

* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC — puste**

  ![Dodaj kontroler MVC i nadaj mu nazwę](adding-controller/_static/ac.png)

* W **oknie dialogowym Dodawanie pustego kontrolera MVC**wprowadź **helloworldcontroller** i wybierz pozycję **ADD**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Wybierz ikonę **EXPLORER,** a następnie kliknij (kliknij prawym przyciskiem myszy) **kontrolery > nowy plik** i nadaj nowy plik *HelloWorldController.cs*.

  ![Menu kontekstowe](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **kontrolery > dodaj > nowy plik**.
![Menu kontekstowe](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Wybierz **ASP.NET klasę kontrolera Core** i **MVC**.

Nazwij kontroler **HelloWorldController**.

![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Zastąp zawartość *controllers/HelloWorldController.cs* na następujące:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Każda `public` metoda w kontrolerze jest wywoływana jako punkt końcowy HTTP. W powyższym przykładzie obie metody zwracają ciąg. Zanotuj komentarze poprzedzające każdą metodę.

Punkt końcowy HTTP jest docelowym adresem URL w `https://localhost:5001/HelloWorld`aplikacji internetowej, takim `HTTPS`jak , i łączy używany protokół: , `localhost:5001` lokalizację sieciową `HelloWorld`serwera sieci web (w tym portu TCP): i docelowy identyfikator URI .

Pierwszy komentarz stwierdza, że jest to metoda [HTTP GET,](https://www.w3schools.com/tags/ref_httpmethods.asp) która jest wywoływana przez dołączenie `/HelloWorld/` do podstawowego adresu URL. Drugi komentarz określa metodę [HTTP GET,](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) która jest wywoływana przez dołączenie `/HelloWorld/Welcome/` do adresu URL. W dalszej części samouczka aparat szkieletu `HTTP POST` jest używany do generowania metod, które aktualizują dane.

Uruchom aplikację w trybie bez debugowania i dołącz "HelloWorld" do ścieżki na pasku adresu. Metoda `Index` zwraca ciąg.

![Okno przeglądarki z odpowiedzią aplikacji Jest to moja domyślna akcja](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC wywołuje klasy kontrolera (i metody akcji w nich) w zależności od przychodzącego adresu URL. Domyślna [logika routingu adresów URL](xref:mvc/controllers/routing) używana przez MVC używa takiego formatu, aby określić, jaki kod ma wywołać:

`/[Controller]/[ActionName]/[Parameters]`

Format routingu jest `Configure` ustawiony w metodzie w *pliku Startup.cs.*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Gdy przeglądasz aplikację i nie dostarczasz żadnych segmentów adresów URL, domyślnie kontroler "Home" i metoda "Indeks" określona w wierszu szablonu wyróżnionym powyżej.

Pierwszy segment adresu URL określa klasę kontrolera do uruchomienia. Więc `localhost:{PORT}/HelloWorld` mapy `HelloWorldController` do klasy. Druga część segmentu ADRESU URL określa metodę akcji w klasie. Tak `localhost:{PORT}/HelloWorld/Index` więc `Index` spowodowałoby, `HelloWorldController` że metoda klasy do uruchomienia. Należy zauważyć, że trzeba `localhost:{PORT}/HelloWorld` było `Index` tylko przeglądać i metoda została wywołana domyślnie. Jest to `Index` spowodowane jest domyślną metodą, która zostanie wywołana na kontrolerze, jeśli nazwa metody nie jest jawnie określona. Trzecia część segmentu adresu `id`URL ( ) dotyczy danych trasy. Dane trasy jest wyjaśnione w dalszej części samouczka.

Przejdź do `https://localhost:{PORT}/HelloWorld/Welcome`. Metoda `Welcome` uruchamia się i `This is the Welcome action method...`zwraca ciąg . Dla tego adresu URL `HelloWorld` `Welcome` kontroler jest i jest metodą akcji. Część adresu URL `[Parameters]` nie została jeszcze użyta.

![Okno przeglądarki z odpowiedzią aplikacji Jest to metoda akcji Powitalna](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Zmodyfikuj kod, aby przekazać niektóre informacje o parametrach z adresu URL do kontrolera. Na przykład `/HelloWorld/Welcome?name=Rick&numtimes=4`. Zmień `Welcome` metodę, aby uwzględnić dwa parametry, jak pokazano w poniższym kodzie.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Powyższy kod ma następujące działanie:

* Używa funkcji parametru opcjonalnego języka C#, aby wskazać, że `numTimes` parametr jest domyślnie 1, jeśli nie jest przekazywana żadna wartość dla tego parametru. <!-- remove for simplified -->
* Służy `HtmlEncoder.Default.Encode` do ochrony aplikacji przed złośliwym wprowadzaniem danych (a mianowicie JavaScript).
* Używa [interpolowanych ciągów](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) w `$"Hello {name}, NumTimes is: {numTimes}"`pliku . <!-- remove for simplified -->

Uruchom aplikację i przejdź do:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Zamień `{PORT}` numer portu). Możesz wypróbować różne `name` wartości `numtimes` dla i w adresie URL. System [wiązania modelu](xref:mvc/models/model-binding) MVC automatycznie mapuje nazwane parametry z ciągu zapytania na pasku adresu do parametrów w metodzie. Zobacz [powiązanie modelu, aby](xref:mvc/models/model-binding) uzyskać więcej informacji.

![Okno przeglądarki pokazujące odpowiedź aplikacji Hello Rick,\: NumTimes jest 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na powyższym obrazie segment`Parameters`adresu URL ( `name` ) `numTimes` nie jest używany, parametry i są przekazywane w [ciągu zapytania](https://wikipedia.org/wiki/Query_string). `?` (Znak zapytania) w powyższym adresie URL jest separatorem, a ciąg zapytania następuje. Znak `&` oddziela pary wartości pola.

Zastąp metodę `Welcome` poniższym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uruchom aplikację i wprowadź następujący adres URL:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tym razem trzeci segment adresu URL `id`odpowiadał parametrowi trasy . Metoda `Welcome` zawiera parametr, `id` który pasował do `MapRoute` szablonu adresu URL w metodzie. Trailing `?` (in) `id?`wskazuje, `id` że parametr jest opcjonalny.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

W tych przykładach kontroler robi "VC" część MVC - czyli widok i kontroler pracy. Kontroler zwraca kod HTML bezpośrednio. Ogólnie nie chcesz kontrolerów zwracanych HTML bezpośrednio, ponieważ staje się to bardzo uciążliwe dla kodu i utrzymania. Zamiast tego zazwyczaj używasz oddzielnego pliku szablonu widoku Razor, aby wygenerować odpowiedź HTML. Można to zrobić w następnym samouczku.

> [!div class="step-by-step"]
> [Poprzedni](start-mvc.md)
> [następny](adding-view.md)

::: moniker-end
