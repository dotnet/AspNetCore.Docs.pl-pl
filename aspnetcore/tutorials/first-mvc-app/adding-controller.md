---
title: Część 2, dodawanie kontrolera do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 2 z serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: 1bb2d96d7b58bdd88ce9c2266c33f6e7de9e9209
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84653032"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a>Część 2, dodawanie kontrolera do aplikacji ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Wzorzec architektoniczny Model-View-Controller (MVC) oddziela aplikację do trzech głównych składników: **M**odelu, **V**każ i **C**ontroller. Wzorzec MVC pomaga tworzyć aplikacje, które są bardziej weryfikowalne i łatwiej aktualizować niż tradycyjne aplikacje monolityczne. Aplikacje oparte na MVC zawierają:

* **M**Odels: klasy reprezentujące dane aplikacji. Klasy modeli używają logiki walidacji, aby wymusić reguły biznesowe dla tych danych. Zazwyczaj obiekty modelu pobierają i przechowują stan modelu w bazie danych. W tym samouczku `Movie` model pobiera dane filmu z bazy danych, udostępnia je widokowi lub aktualizuje. Zaktualizowane dane są zapisywane w bazie danych.

* **V**Iews: widoki są składnikami, które wyświetlają interfejs użytkownika aplikacji. Zazwyczaj ten interfejs użytkownika wyświetla dane modelu.

* **C**Ontrollers: klasy, które obsługują żądania przeglądarki. Pobierają one dane modelu i szablony widoków wywołań, które zwracają odpowiedź. W aplikacji MVC widok wyświetla tylko informacje; kontroler obsługuje i reaguje na dane wejściowe użytkownika i interakcje. Na przykład kontroler obsługuje dane tras i wartości ciągu zapytania i przekazuje te wartości do modelu. Model może używać tych wartości do wykonywania zapytań w bazie danych. Na przykład `https://localhost:5001/Home/Privacy` ma dane trasy `Home` (kontroler) i `Privacy` (Metoda akcji do wywołania na kontrolerze głównym). `https://localhost:5001/Movies/Edit/5`jest żądaniem edycji filmu o IDENTYFIKATORze 5 przy użyciu kontrolera filmu. Dane trasy są wyjaśnione w dalszej części samouczka.

Wzorzec MVC ułatwia tworzenie aplikacji, które oddzielają różne aspekty aplikacji (logiki wejściowej, logiki biznesowej i logiki interfejsu użytkownika), jednocześnie zapewniając swobodny sprzężenie między tymi elementami. Wzorzec określa, gdzie poszczególne rodzaje logiki powinny znajdować się w aplikacji. Logika interfejsu użytkownika należy do widoku. Logika wejściowa należy do kontrolera. Logika biznesowa należy do modelu. Ta separacja ułatwia zarządzanie złożonością podczas kompilowania aplikacji, ponieważ umożliwia pracę nad jednym aspektem implementacji jednocześnie bez wpływu na kod innego. Na przykład można korzystać z kodu widoku bez w zależności od kodu logiki biznesowej.

Omawiamy te koncepcje w tej serii samouczków i pokazano, jak używać ich do kompilowania aplikacji filmowej. Projekt MVC zawiera foldery dla *kontrolerów* i *widoków*.

## <a name="add-a-controller"></a>Dodawanie kontrolera

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **Kontrolery > Dodaj** 
   ![ menu kontekstowe kontrolera >](adding-controller/_static/add_controller.png)

* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC — pusty**

  ![Dodaj kontroler MVC i nadaj mu nazwę](adding-controller/_static/ac.png)

* W **oknie dialogowym Dodaj pusty kontroler MVC**wpisz **HelloWorldController** i wybierz pozycję **Dodaj**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Wybierz ikonę **Eksploratora** , a następnie kliknij przycisk Control (kliknij prawym przyciskiem myszy) **Kontrolery > nowy plik** i Nadaj nowemu plikowi nazwę *HelloWorldController.cs*.

  ![Menu kontekstowe](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **kontrolery > Dodaj > nowy plik**.
![Menu kontekstowe](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Wybierz klasę **ASP.NET Core** i **kontrolera MVC**.

Nadaj nazwę kontrolerowi **HelloWorldController**.

![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Zastąp zawartość *controllers/HelloWorldController. cs* następującymi kwestiami:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Każda `public` Metoda w kontrolerze jest wywoływana jako punkt końcowy HTTP. W powyższym przykładzie obie metody zwracają ciąg. Zwróć uwagę na komentarze poprzedzające każdą metodę.

Punkt końcowy HTTP to docelowy adres URL w aplikacji sieci Web, na przykład `https://localhost:5001/HelloWorld` i łączy używany protokół: `HTTPS` , lokalizację sieciową serwera sieci Web (w tym port TCP): `localhost:5001` i docelowy identyfikator URI `HelloWorld` .

Pierwszy komentarz wskazuje, że jest to metoda [http Get](https://www.w3schools.com/tags/ref_httpmethods.asp) , która jest wywoływana przez dołączenie `/HelloWorld/` do podstawowego adresu URL. Drugi komentarz określa metodę [http Get](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) , która jest wywoływana przez dołączenie `/HelloWorld/Welcome/` do adresu URL. W dalszej części tego samouczka aparat szkieletu służy do generowania `HTTP POST` metod, które aktualizują dane.

Uruchom aplikację w trybie innym niż debugowanie i Dołącz do niej plik "HelloWorld" do ścieżki na pasku adresu. `Index`Metoda zwraca ciąg.

![Okno przeglądarki pokazujące odpowiedź aplikacji to moja domyślna akcja](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC wywołuje klasy kontrolera (i w nich metody akcji) w zależności od przychodzącego adresu URL. Domyślna [logika routingu adresów URL](xref:mvc/controllers/routing) używana przez MVC korzysta z formatu takiego jak ten, aby określić kod, który ma zostać wywołany:

`/[Controller]/[ActionName]/[Parameters]`

Format routingu jest ustawiany w `Configure` metodzie w pliku *Startup.cs* .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Gdy przejdziesz do aplikacji i nie podasz żadnych segmentów adresu URL, domyślnym kontrolerem "Home" i metodą "index" określoną w wierszu szablonu wyróżnionym powyżej.

Pierwszy segment adresu URL określa klasę kontrolera do uruchomienia. Dlatego `localhost:{PORT}/HelloWorld` mapuje do klasy kontrolera **HelloWorld**. Druga część segmentu adresu URL określa metodę akcji klasy. `localhost:{PORT}/HelloWorld/Index`Mogłoby to spowodować `Index` uruchomienie metody `HelloWorldController` klasy. Zwróć uwagę, że trzeba tylko przeglądać do `localhost:{PORT}/HelloWorld` i `Index` Metoda została wywołana domyślnie. Dzieje się tak dlatego, że `Index` jest to metoda domyślna, która będzie wywoływana na kontrolerze, jeśli nazwa metody nie jest jawnie określona. Trzecia część segmentu URL ( `id` ) jest dla danych trasy. Dane trasy są wyjaśnione w dalszej części samouczka.

Przejdź do `https://localhost:{PORT}/HelloWorld/Welcome`. `Welcome`Metoda jest uruchamiana i zwraca ciąg `This is the Welcome action method...` . Dla tego adresu URL kontroler jest `HelloWorld` i `Welcome` jest metodą akcji. Nie użyto `[Parameters]` jeszcze części adresu URL.

![Okno przeglądarki pokazujące odpowiedź aplikacji jest to metoda akcji powitalnej](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Zmodyfikuj kod, aby przekazać do kontrolera informacje o parametrach z adresu URL. Na przykład `/HelloWorld/Welcome?name=Rick&numtimes=4`. Zmień `Welcome` metodę, tak aby obejmowała dwa parametry, jak pokazano w poniższym kodzie.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Powyższy kod ma następujące działanie:

* Używa funkcji opcjonalnego parametru języka C#, aby wskazać, że `numTimes` parametr domyślnie przyjmuje wartość 1, jeśli dla tego parametru nie jest przenoszona żadna wartooć. <!-- remove for simplified -->
* Program używa programu `HtmlEncoder.Default.Encode` do ochrony aplikacji przed złośliwymi danymi wejściowymi (tj. JavaScript).
* Używa [interpolowanych ciągów](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) w `$"Hello {name}, NumTimes is: {numTimes}"` . <!-- remove for simplified -->

Uruchom aplikację i przejdź do:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Zamień `{PORT}` na numer portu). Możesz wypróbować różne wartości dla `name` i `numtimes` w adresie URL. System [powiązania modelu](xref:mvc/models/model-binding) MVC automatycznie mapuje nazwane parametry z ciągu zapytania na pasku adresu na parametry w metodzie. Aby uzyskać więcej informacji, zobacz [powiązanie modelu](xref:mvc/models/model-binding) .

![Okno przeglądarki pokazujące odpowiedź aplikacji Hello Rick, NumTimes jest \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na powyższym obrazie segment adresu URL ( `Parameters` ) nie jest używany, `name` a `numTimes` Parametry i są przesyłane w [ciągu zapytania](https://wikipedia.org/wiki/Query_string). `?`(Znak zapytania) w powyższym adresie URL jest separatorem, a ciąg zapytania znajduje się poniżej. `&`Znak oddziela pary pól i wartości.

Zastąp metodę `Welcome` poniższym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uruchom aplikację i wprowadź następujący adres URL:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tym razem trzeci segment adresu URL pasuje do parametru Route `id` . `Welcome`Metoda zawiera parametr `id` , który jest zgodny z szablonem adresu URL w `MapControllerRoute` metodzie. Końcowe `?` (w `id?` ) wskazuje, że `id` parametr jest opcjonalny.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

W tych przykładach kontroler wykonywał część "VC" MVC, czyli każ **V**i **C**ontroller Work. Kontroler zwraca bezpośrednio kod HTML. Zazwyczaj nie ma potrzeby, aby kontrolery zwracające kod HTML bezpośrednio, ponieważ staną się bardzo nieskomplikowane w kodzie i obsłudze. Zamiast tego zwykle używasz osobnego Razor pliku szablonu widoku do wygenerowania odpowiedzi html. Należy to zrobić w następnym samouczku.

> [!div class="step-by-step"]
> [Poprzedni](start-mvc.md) 
>  [Dalej](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Wzorzec architektoniczny Model-View-Controller (MVC) oddziela aplikację do trzech głównych składników: **M**odelu, **V**każ i **C**ontroller. Wzorzec MVC pomaga tworzyć aplikacje, które są bardziej weryfikowalne i łatwiej aktualizować niż tradycyjne aplikacje monolityczne. Aplikacje oparte na MVC zawierają:

* **M**Odels: klasy reprezentujące dane aplikacji. Klasy modeli używają logiki walidacji, aby wymusić reguły biznesowe dla tych danych. Zazwyczaj obiekty modelu pobierają i przechowują stan modelu w bazie danych. W tym samouczku `Movie` model pobiera dane filmu z bazy danych, udostępnia je widokowi lub aktualizuje. Zaktualizowane dane są zapisywane w bazie danych.

* **V**Iews: widoki są składnikami, które wyświetlają interfejs użytkownika aplikacji. Zazwyczaj ten interfejs użytkownika wyświetla dane modelu.

* **C**Ontrollers: klasy, które obsługują żądania przeglądarki. Pobierają one dane modelu i szablony widoków wywołań, które zwracają odpowiedź. W aplikacji MVC widok wyświetla tylko informacje; kontroler obsługuje i reaguje na dane wejściowe użytkownika i interakcje. Na przykład kontroler obsługuje dane tras i wartości ciągu zapytania i przekazuje te wartości do modelu. Model może używać tych wartości do wykonywania zapytań w bazie danych. Na przykład `https://localhost:5001/Home/About` ma dane trasy `Home` (kontroler) i `About` (Metoda akcji do wywołania na kontrolerze głównym). `https://localhost:5001/Movies/Edit/5`jest żądaniem edycji filmu o IDENTYFIKATORze 5 przy użyciu kontrolera filmu. Dane trasy są wyjaśnione w dalszej części samouczka.

Wzorzec MVC ułatwia tworzenie aplikacji, które oddzielają różne aspekty aplikacji (logiki wejściowej, logiki biznesowej i logiki interfejsu użytkownika), jednocześnie zapewniając swobodny sprzężenie między tymi elementami. Wzorzec określa, gdzie poszczególne rodzaje logiki powinny znajdować się w aplikacji. Logika interfejsu użytkownika należy do widoku. Logika wejściowa należy do kontrolera. Logika biznesowa należy do modelu. Ta separacja ułatwia zarządzanie złożonością podczas kompilowania aplikacji, ponieważ umożliwia pracę nad jednym aspektem implementacji jednocześnie bez wpływu na kod innego. Na przykład można korzystać z kodu widoku bez w zależności od kodu logiki biznesowej.

Omawiamy te koncepcje w tej serii samouczków i pokazano, jak używać ich do kompilowania aplikacji filmowej. Projekt MVC zawiera foldery dla *kontrolerów* i *widoków*.

## <a name="add-a-controller"></a>Dodawanie kontrolera

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **Kontrolery > Dodaj** 
   ![ menu kontekstowe kontrolera >](adding-controller/_static/add_controller.png)

* W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC — pusty**

  ![Dodaj kontroler MVC i nadaj mu nazwę](adding-controller/_static/ac.png)

* W **oknie dialogowym Dodaj pusty kontroler MVC**wpisz **HelloWorldController** i wybierz pozycję **Dodaj**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Wybierz ikonę **Eksploratora** , a następnie kliknij przycisk Control (kliknij prawym przyciskiem myszy) **Kontrolery > nowy plik** i Nadaj nowemu plikowi nazwę *HelloWorldController.cs*.

  ![Menu kontekstowe](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **kontrolery > Dodaj > nowy plik**.
![Menu kontekstowe](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Wybierz klasę **ASP.NET Core** i **kontrolera MVC**.

Nadaj nazwę kontrolerowi **HelloWorldController**.

![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Zastąp zawartość *controllers/HelloWorldController. cs* następującymi kwestiami:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Każda `public` Metoda w kontrolerze jest wywoływana jako punkt końcowy HTTP. W powyższym przykładzie obie metody zwracają ciąg. Zwróć uwagę na komentarze poprzedzające każdą metodę.

Punkt końcowy HTTP to docelowy adres URL w aplikacji sieci Web, na przykład `https://localhost:5001/HelloWorld` i łączy używany protokół: `HTTPS` , lokalizację sieciową serwera sieci Web (w tym port TCP): `localhost:5001` i docelowy identyfikator URI `HelloWorld` .

Pierwszy komentarz wskazuje, że jest to metoda [http Get](https://www.w3schools.com/tags/ref_httpmethods.asp) , która jest wywoływana przez dołączenie `/HelloWorld/` do podstawowego adresu URL. Drugi komentarz określa metodę [http Get](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) , która jest wywoływana przez dołączenie `/HelloWorld/Welcome/` do adresu URL. W dalszej części tego samouczka aparat szkieletu służy do generowania `HTTP POST` metod, które aktualizują dane.

Uruchom aplikację w trybie innym niż debugowanie i Dołącz do niej plik "HelloWorld" do ścieżki na pasku adresu. `Index`Metoda zwraca ciąg.

![Okno przeglądarki pokazujące odpowiedź aplikacji to moja domyślna akcja](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC wywołuje klasy kontrolera (i w nich metody akcji) w zależności od przychodzącego adresu URL. Domyślna [logika routingu adresów URL](xref:mvc/controllers/routing) używana przez MVC korzysta z formatu takiego jak ten, aby określić kod, który ma zostać wywołany:

`/[Controller]/[ActionName]/[Parameters]`

Format routingu jest ustawiany w `Configure` metodzie w pliku *Startup.cs* .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Gdy przejdziesz do aplikacji i nie podasz żadnych segmentów adresu URL, domyślnym kontrolerem "Home" i metodą "index" określoną w wierszu szablonu wyróżnionym powyżej.

Pierwszy segment adresu URL określa klasę kontrolera do uruchomienia. Dlatego `localhost:{PORT}/HelloWorld` mapuje do `HelloWorldController` klasy. Druga część segmentu adresu URL określa metodę akcji klasy. `localhost:{PORT}/HelloWorld/Index`Mogłoby to spowodować `Index` uruchomienie metody `HelloWorldController` klasy. Zwróć uwagę, że trzeba tylko przeglądać do `localhost:{PORT}/HelloWorld` i `Index` Metoda została wywołana domyślnie. Jest tak dlatego `Index` , że jest to metoda domyślna, która będzie wywoływana na kontrolerze, jeśli nazwa metody nie jest jawnie określona. Trzecia część segmentu URL ( `id` ) jest dla danych trasy. Dane trasy są wyjaśnione w dalszej części samouczka.

Przejdź do `https://localhost:{PORT}/HelloWorld/Welcome`. `Welcome`Metoda jest uruchamiana i zwraca ciąg `This is the Welcome action method...` . Dla tego adresu URL kontroler jest `HelloWorld` i `Welcome` jest metodą akcji. Nie użyto `[Parameters]` jeszcze części adresu URL.

![Okno przeglądarki pokazujące odpowiedź aplikacji jest to metoda akcji powitalnej](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Zmodyfikuj kod, aby przekazać do kontrolera informacje o parametrach z adresu URL. Na przykład `/HelloWorld/Welcome?name=Rick&numtimes=4`. Zmień `Welcome` metodę, tak aby obejmowała dwa parametry, jak pokazano w poniższym kodzie.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Powyższy kod ma następujące działanie:

* Używa funkcji opcjonalnego parametru języka C#, aby wskazać, że `numTimes` parametr domyślnie przyjmuje wartość 1, jeśli dla tego parametru nie jest przenoszona żadna wartooć. <!-- remove for simplified -->
* Program używa programu `HtmlEncoder.Default.Encode` do ochrony aplikacji przed złośliwymi danymi wejściowymi (tj. JavaScript).
* Używa [interpolowanych ciągów](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) w `$"Hello {name}, NumTimes is: {numTimes}"` . <!-- remove for simplified -->

Uruchom aplikację i przejdź do:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Zamień `{PORT}` na numer portu). Możesz wypróbować różne wartości dla `name` i `numtimes` w adresie URL. System [powiązania modelu](xref:mvc/models/model-binding) MVC automatycznie mapuje nazwane parametry z ciągu zapytania na pasku adresu na parametry w metodzie. Aby uzyskać więcej informacji, zobacz [powiązanie modelu](xref:mvc/models/model-binding) .

![Okno przeglądarki pokazujące odpowiedź aplikacji Hello Rick, NumTimes jest \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na powyższym obrazie segment adresu URL ( `Parameters` ) nie jest używany, `name` a `numTimes` Parametry i są przesyłane w [ciągu zapytania](https://wikipedia.org/wiki/Query_string). `?`(Znak zapytania) w powyższym adresie URL jest separatorem, a ciąg zapytania znajduje się poniżej. `&`Znak oddziela pary pól i wartości.

Zastąp metodę `Welcome` poniższym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Uruchom aplikację i wprowadź następujący adres URL:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tym razem trzeci segment adresu URL pasuje do parametru Route `id` . `Welcome`Metoda zawiera parametr `id` , który jest zgodny z szablonem adresu URL w `MapRoute` metodzie. Końcowe `?` (w `id?` ) wskazuje, że `id` parametr jest opcjonalny.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

W tych przykładach kontroler wykonywał część "VC" składnika MVC, czyli działania widoku i kontrolera. Kontroler zwraca bezpośrednio kod HTML. Zazwyczaj nie ma potrzeby, aby kontrolery zwracające kod HTML bezpośrednio, ponieważ staną się bardzo nieskomplikowane w kodzie i obsłudze. Zamiast tego zwykle używasz oddzielnego Razor pliku szablonu widoku, aby ułatwić generowanie odpowiedzi html. Należy to zrobić w następnym samouczku.

> [!div class="step-by-step"]
> [Poprzedni](start-mvc.md) 
>  [Dalej](adding-view.md)

::: moniker-end
