---
title: Dodawanie widoku do aplikacji core mvc ASP.NET
author: rick-anderson
description: Dodawanie widoku do prostej aplikacji Core MVC ASP.NET
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660210"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>Dodawanie widoku do aplikacji core mvc ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

W tej sekcji `HelloWorldController` można zmodyfikować klasy za pomocą [razor](xref:mvc/views/razor) plików widoku do czyszczenia hermetyzować proces generowania odpowiedzi HTML do klienta.

Plik szablonu widoku można utworzyć za pomocą razor. Szablony widoku oparte na brzytwie mają rozszerzenie pliku *cshtml.* Zapewniają one elegancki sposób tworzenia danych wyjściowych HTML z języka C#.

Obecnie `Index` metoda zwraca ciąg z komunikatem, który jest zakodowany na stałe w klasie kontrolera. W `HelloWorldController` klasie zastąp `Index` metodę następującym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Poprzedni kod wywołuje metodę kontrolera. <xref:Microsoft.AspNetCore.Mvc.Controller.View*> Używa szablonu widoku do generowania odpowiedzi HTML. Metody kontrolera (znane również jako `Index` *metody akcji),* takie <xref:Microsoft.AspNetCore.Mvc.IActionResult> jak powyższa metoda, <xref:Microsoft.AspNetCore.Mvc.ActionResult>zazwyczaj zwracają `string`(lub klasę pochodną), a nie typ podobny.

## <a name="add-a-view"></a>Dodawanie widoku

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.

* Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy element**.

* W oknie dialogowym **Dodaj nowy przedmiot — MvcMovie**

  * W polu wyszukiwania w prawym górnym rogu wprowadź *widok*

  * Wybierz **widok razor**

  * Zachowaj wartość pola **Nazwa,** *Index.cshtml*.

  * Wybierz **pozycję Dodaj**

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Dodaj `Index` widok dla `HelloWorldController`pliku .

* Dodaj nowy folder o nazwie *Widoki/HelloWorld*.
* Dodaj nowy plik do nazwy folderu *Widoki/HelloWorld* *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.
* Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy plik**.
* W oknie dialogowym **Nowy plik:**

  * Wybierz **pozycję ASP .NET Core** w lewym okienku.
  * Wybierz **pozycję Strona widoku MVC** w środkowym okienku.
  * Wpisz *indeks* w polu **Nazwa.**
  * Wybierz **pozycję Nowy**.

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_mac.png)

---

Zastąp zawartość pliku widoku *Widoki/HelloWorld/Index.cshtml* Razor na następujące elementy:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Przejdź do adresu `https://localhost:{PORT}/HelloWorld`. Metoda `Index` w `HelloWorldController` nie zrobił wiele; uruchomiono instrukcję `return View();`, która określiła, że metoda powinna używać pliku szablonu widoku do renderowania odpowiedzi na przeglądarkę. Ponieważ nie określono nazwy pliku szablonu widoku, domyślnie MVC używał domyślnego pliku widoku. Domyślny plik widoku ma taką samą nazwę jak metoda (`Index`), więc w */Views/HelloWorld/Index.cshtml* jest używany. Na poniższej ilustracji znajduje się napis "Hello from our View Template!" zakodowane w widoku.

![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Zmienianie widoków i stron układu

Wybierz łącza menu **(MvcMovie**, **Home**i **Prywatność).** Każda strona zawiera ten sam układ menu. Układ menu jest zaimplementowany w pliku *Views/Shared/_Layout.cshtml.* Otwórz plik *Views/Shared/_Layout.cshtml.*

[Szablony układu](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie. Znajdź `@RenderBody()` linię. `RenderBody`to symbol zastępczy, w którym wszystkie utworzone strony specyficzne dla widoku są wyświetlane, *zawinięte* na stronie układu. Jeśli na przykład wybierzesz łącze **Prywatność,** widok **Widoki/Strona główna/Prywatność.cshtml** zostanie renderowany wewnątrz `RenderBody` metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Zmienianie tytułu, stopki i łącza menu w pliku układu

Zastąp zawartość pliku *Views/Shared/_Layout.cshtml* następującymi znacznikami. Zmiany są wyróżnione:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

Poprzedni znacznik dokonał następujących zmian:

* 3 wystąpienia `MvcMovie` do `Movie App`.
* Element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` zakotwiczenia do `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

W poprzednim znacznikach pominięto atrybut i wartość atrybutu `asp-area=""` [Pomocnika znaczników zakotwiczenia,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ponieważ ta aplikacja nie używa [funkcji Obszary](xref:mvc/controllers/areas).

**Uwaga:** `Movies` Kontroler nie został zaimplementowany. W tym momencie łącze `Movie App` nie jest funkcjonalne.

Zapisz zmiany i wybierz link **Prywatność.** Zwróć uwagę, jak tytuł na karcie przeglądarki wyświetla **Politykę prywatności - Movie App** zamiast Polityki prywatności - **Mvc Movie**:

![Karta Prywatność](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Zaznacz łącze **Strona główna** i zwróć uwagę, że tytuł i tekst zakotwiczenia wyświetlają również aplikację **Movie App**. Byliśmy w stanie wprowadzić zmianę raz w szablonie układu i wszystkie strony w witrynie odzwierciedlają nowy tekst linku i nowy tytuł.

Sprawdź plik *Views/_ViewStart.cshtml:*

```cshtml
@{
    Layout = "_Layout";
}
```

Plik *Views/_ViewStart.cshtml* wprowadza do każdego widoku plik *Views/Shared/_Layout.cshtml.* Właściwości `Layout` może służyć do ustawiania innego widoku układu `null` lub ustawić go na tak, aby nie plik układu będą używane.

Zmień tytuł `<h2>` i element pliku widoku *Widoki/HelloWorld/Index.cshtml:*

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Tytuł i `<h2>` element są nieco inne, dzięki czemu można zobaczyć, który bit kodu zmienia wyświetlanie.

`ViewData["Title"] = "Movie List";`w powyższym kodzie `Title` ustawia `ViewData` właściwość słownika na "Lista filmów". Właściwość `Title` jest używana `<title>` w elemencie HTML na stronie układu:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Zapisz zmianę i `https://localhost:{PORT}/HelloWorld`przejdź do . Należy zauważyć, że zmienił się tytuł przeglądarki, nagłówek podstawowy i nagłówki pomocnicze. (Jeśli nie widzisz zmian w przeglądarce, być może wyświetlasz zawartość w pamięci podręcznej. Naciśnij klawisze Ctrl+F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera). Tytuł przeglądarki jest `ViewData["Title"]` tworzony z zestawem w szablonie widoku *Index.cshtml* i dodatkowym "- Movie App" dodanym w pliku układu.

Zawartość szablonu widoku *Index.cshtml* jest scalana z szablonem widoku *Widoki/Udostępnione/_Layout.cshtml.* Pojedyncza odpowiedź HTML jest wysyłana do przeglądarki. Szablony układu ułatwiają wprowadzanie zmian, które mają zastosowanie do wszystkich stron w aplikacji. Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Nasze trochę "danych" (w tym przypadku "Hello from our View Template!" komunikat) jest jednak zakodowany na twardo. Aplikacja MVC ma "V" (widok) i masz "C" (kontroler), ale nie "M" (model) jeszcze.

## <a name="passing-data-from-the-controller-to-the-view"></a>Przekazywanie danych z kontrolera do widoku

Akcje kontrolera są wywoływane w odpowiedzi na przychodzące żądanie adresu URL. Klasa kontrolera jest, gdy kod jest zapisywany, który obsługuje przychodzące żądania przeglądarki. Kontroler pobiera dane ze źródła danych i decyduje, jaki typ odpowiedzi ma być wysyłany z powrotem do przeglądarki. Szablony widoku mogą być używane z kontrolera do generowania i formatowania odpowiedzi HTML do przeglądarki.

Kontrolerzy są odpowiedzialni za dostarczanie danych wymaganych do szablonu widoku do renderowania odpowiedzi. Najlepsze rozwiązanie: Wyświetlanie szablonów **nie** należy wykonywać logiki biznesowej lub bezpośrednio wchodzić w interakcje z bazą danych. Zamiast tego szablon widoku powinien działać tylko z danymi dostarczonymi do niego przez kontrolera. Utrzymanie tego "separacji problemów" pomaga zachować kod czyste, sprawdzalne i chywalne.

Obecnie `Welcome` metoda w `HelloWorldController` klasie przyjmuje `name` i `ID` parametr, a następnie wyprowadza wartości bezpośrednio do przeglądarki. Zamiast kontrolera renderowania tej odpowiedzi jako ciąg, należy zmienić kontroler, aby zamiast tego użyć szablonu widoku. Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie bity danych muszą być przekazywane z kontrolera do widoku w celu wygenerowania odpowiedzi. W tym celu kontroler umieszcza dane dynamiczne (parametry), których `ViewData` szablon widoku potrzebuje w słowniku, do który szablon widoku może uzyskać dostęp.

W *HelloWorldController.cs*, zmień `Welcome` metodę, aby `Message` `NumTimes` dodać i `ViewData` wartość do słownika. Słownik `ViewData` jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu; `ViewData` obiekt nie ma zdefiniowanych właściwości, dopóki nie umieścisz w nim czegoś. [System wiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje`name` `numTimes`nazwane parametry ( i ) z ciągu zapytania na pasku adresu do parametrów w metodzie. Kompletny plik *HelloWorldController.cs* wygląda następująco:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Obiekt `ViewData` słownika zawiera dane, które zostaną przekazane do widoku.

Utwórz szablon widoku powitalnego o nazwie *Widoki/HelloWorld/Welcome.cshtml*.

Utworzysz pętlę w szablonie widoku *Welcome.cshtml* `NumTimes`z wyświetlonym programem "Hello". Zastąp zawartość *Views/HelloWorld/Welcome.cshtml* następującymi:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Zapisz zmiany i przejdź do następującego adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Dane są pobierane z adresu URL i przekazywane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding) . Kontroler pakiety danych `ViewData` do słownika i przekazuje ten obiekt do widoku. Widok następnie renderuje dane w formacie HTML do przeglądarki.

![Widok prywatności przedstawiający etykietę powitalną i wyrażenie Hello Rick pokazane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

W powyższym przykładzie `ViewData` słownik został użyty do przekazywania danych z kontrolera do widoku. W dalszej części samouczka model widoku jest używany do przekazywania danych z kontrolera do widoku. Podejście modelu widoku do przekazywania danych jest `ViewData` ogólnie znacznie preferowane w stosunku do podejścia słownika. Aby uzyskać więcej [informacji, zobacz Kiedy używać viewbag, ViewData lub TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)

W następnym samouczku tworzona jest baza danych filmów.

> [!div class="step-by-step"]
> [Poprzedni](adding-controller.md)
> [następny](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tej sekcji `HelloWorldController` można zmodyfikować klasy za pomocą [razor](xref:mvc/views/razor) plików widoku do czyszczenia hermetyzować proces generowania odpowiedzi HTML do klienta.

Plik szablonu widoku można utworzyć za pomocą razor. Szablony widoku oparte na brzytwie mają rozszerzenie pliku *cshtml.* Zapewniają one elegancki sposób tworzenia danych wyjściowych HTML z języka C#.

Obecnie `Index` metoda zwraca ciąg z komunikatem, który jest zakodowany na stałe w klasie kontrolera. W `HelloWorldController` klasie zastąp `Index` metodę następującym kodem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Poprzedni kod wywołuje metodę kontrolera. <xref:Microsoft.AspNetCore.Mvc.Controller.View*> Używa szablonu widoku do generowania odpowiedzi HTML. Metody kontrolera (znane również jako `Index` *metody akcji),* takie <xref:Microsoft.AspNetCore.Mvc.IActionResult> jak powyższa metoda, <xref:Microsoft.AspNetCore.Mvc.ActionResult>zazwyczaj zwracają `string`(lub klasę pochodną), a nie typ podobny.

## <a name="add-a-view"></a>Dodawanie widoku

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.

* Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy element**.

* W oknie dialogowym **Dodaj nowy przedmiot — MvcMovie**

  * W polu wyszukiwania w prawym górnym rogu wprowadź *widok*

  * Wybierz **widok razor**

  * Zachowaj wartość pola **Nazwa,** *Index.cshtml*.

  * Wybierz **pozycję Dodaj**

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Dodaj `Index` widok dla `HelloWorldController`pliku .

* Dodaj nowy folder o nazwie *Widoki/HelloWorld*.
* Dodaj nowy plik do nazwy folderu *Widoki/HelloWorld* *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *Widoki,* a następnie **dodaj > nowy folder** i nazwij folder *HelloWorld*.
* Kliknij prawym przyciskiem myszy folder *Widoki/HelloWorld,* a następnie **dodaj > nowy plik**.
* W oknie dialogowym **Nowy plik:**

  * Wybierz **pozycję Sieć Web** w lewym okienku.
  * Wybierz **pozycję Pusty plik HTML** w środkowym okienku.
  * Wpisz *index.cshtml* w polu **Nazwa.**
  * Wybierz **pozycję Nowy**.

![Okno dialogowe Dodawanie nowego elementu](adding-view/_static/add_view_mac.png)

---

Zastąp zawartość pliku widoku *Widoki/HelloWorld/Index.cshtml* Razor na następujące elementy:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Przejdź do adresu `https://localhost:{PORT}/HelloWorld`. Metoda `Index` w `HelloWorldController` nie zrobił wiele; uruchomiono instrukcję `return View();`, która określiła, że metoda powinna używać pliku szablonu widoku do renderowania odpowiedzi na przeglądarkę. Ponieważ nie określono nazwy pliku szablonu widoku, domyślnie MVC używał domyślnego pliku widoku. Domyślny plik widoku ma taką samą nazwę jak metoda (`Index`), więc w */Views/HelloWorld/Index.cshtml* jest używany. Na poniższej ilustracji znajduje się napis "Hello from our View Template!" zakodowane w widoku.

![Okno przeglądarki](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Zmienianie widoków i stron układu

Wybierz łącza menu **(MvcMovie**, **Home**i **Prywatność).** Każda strona zawiera ten sam układ menu. Układ menu jest zaimplementowany w pliku *Views/Shared/_Layout.cshtml.* Otwórz plik *Views/Shared/_Layout.cshtml.*

[Szablony układu](xref:mvc/views/layout) umożliwiają określenie układu kontenera HTML witryny w jednym miejscu, a następnie zastosowanie go na wielu stronach w witrynie. Znajdź `@RenderBody()` linię. `RenderBody`to symbol zastępczy, w którym wszystkie utworzone strony specyficzne dla widoku są wyświetlane, *zawinięte* na stronie układu. Jeśli na przykład wybierzesz łącze **Prywatność,** widok **Widoki/Strona główna/Prywatność.cshtml** zostanie renderowany wewnątrz `RenderBody` metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Zmienianie tytułu, stopki i łącza menu w pliku układu

* W elementów tytułu i `MvcMovie` stopki zmień na `Movie App`.
* Zmień element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` zakotwiczenia na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Następujące znaczniki pokazuje wyróżnione zmiany:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

W poprzednim znaczników atrybut `asp-area` [zakotwiczenia Tag Helper został pominięty,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ponieważ ta aplikacja nie używa [obszarów](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Uwaga:** `Movies` Kontroler nie został zaimplementowany. W tym momencie łącze `Movie App` nie jest funkcjonalne.

Zapisz zmiany i wybierz link **Prywatność.** Zwróć uwagę, jak tytuł na karcie przeglądarki wyświetla **Politykę prywatności - Movie App** zamiast Polityki prywatności - **Mvc Movie**:

![Karta Prywatność](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Zaznacz łącze **Strona główna** i zwróć uwagę, że tytuł i tekst zakotwiczenia wyświetlają również aplikację **Movie App**. Byliśmy w stanie wprowadzić zmianę raz w szablonie układu i wszystkie strony w witrynie odzwierciedlają nowy tekst linku i nowy tytuł.

Sprawdź plik *Views/_ViewStart.cshtml:*

```cshtml
@{
    Layout = "_Layout";
}
```

Plik *Views/_ViewStart.cshtml* wprowadza do każdego widoku plik *Views/Shared/_Layout.cshtml.* Właściwości `Layout` może służyć do ustawiania innego widoku układu `null` lub ustawić go na tak, aby nie plik układu będą używane.

Zmień tytuł `<h2>` i element pliku widoku *Widoki/HelloWorld/Index.cshtml:*

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Tytuł i `<h2>` element są nieco inne, dzięki czemu można zobaczyć, który bit kodu zmienia wyświetlanie.

`ViewData["Title"] = "Movie List";`w powyższym kodzie `Title` ustawia `ViewData` właściwość słownika na "Lista filmów". Właściwość `Title` jest używana `<title>` w elemencie HTML na stronie układu:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Zapisz zmianę i `https://localhost:{PORT}/HelloWorld`przejdź do . Należy zauważyć, że zmienił się tytuł przeglądarki, nagłówek podstawowy i nagłówki pomocnicze. (Jeśli nie widzisz zmian w przeglądarce, być może wyświetlasz zawartość w pamięci podręcznej. Naciśnij klawisze Ctrl+F5 w przeglądarce, aby wymusić załadowanie odpowiedzi z serwera). Tytuł przeglądarki jest `ViewData["Title"]` tworzony z zestawem w szablonie widoku *Index.cshtml* i dodatkowym "- Movie App" dodanym w pliku układu.

Zwróć również uwagę na sposób scalania zawartości szablonu widoku *Index.cshtml* z szablonem widoku *Widoki/Udostępnione/_Layout.cshtml* i wysłano do przeglądarki pojedynczą odpowiedź HTML. Szablony układu ułatwiają wprowadzanie zmian, które mają zastosowanie do wszystkich stron w aplikacji. Aby dowiedzieć się więcej, zobacz [Układ](xref:mvc/views/layout).

![Widok listy filmów](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Nasze trochę "danych" (w tym przypadku "Hello from our View Template!" komunikat) jest jednak zakodowany na twardo. Aplikacja MVC ma "V" (widok) i masz "C" (kontroler), ale nie "M" (model) jeszcze.

## <a name="passing-data-from-the-controller-to-the-view"></a>Przekazywanie danych z kontrolera do widoku

Akcje kontrolera są wywoływane w odpowiedzi na przychodzące żądanie adresu URL. Klasa kontrolera jest, gdy kod jest zapisywany, który obsługuje przychodzące żądania przeglądarki. Kontroler pobiera dane ze źródła danych i decyduje, jaki typ odpowiedzi ma być wysyłany z powrotem do przeglądarki. Szablony widoku mogą być używane z kontrolera do generowania i formatowania odpowiedzi HTML do przeglądarki.

Kontrolerzy są odpowiedzialni za dostarczanie danych wymaganych do szablonu widoku do renderowania odpowiedzi. Najlepsze rozwiązanie: Wyświetlanie szablonów **nie** należy wykonywać logiki biznesowej lub bezpośrednio wchodzić w interakcje z bazą danych. Zamiast tego szablon widoku powinien działać tylko z danymi dostarczonymi do niego przez kontrolera. Utrzymanie tego "separacji problemów" pomaga zachować kod czyste, sprawdzalne i chywalne.

Obecnie `Welcome` metoda w `HelloWorldController` klasie przyjmuje `name` i `ID` parametr, a następnie wyprowadza wartości bezpośrednio do przeglądarki. Zamiast kontrolera renderowania tej odpowiedzi jako ciąg, należy zmienić kontroler, aby zamiast tego użyć szablonu widoku. Szablon widoku generuje odpowiedź dynamiczną, co oznacza, że odpowiednie bity danych muszą być przekazywane z kontrolera do widoku w celu wygenerowania odpowiedzi. W tym celu kontroler umieszcza dane dynamiczne (parametry), których `ViewData` szablon widoku potrzebuje w słowniku, do który szablon widoku może uzyskać dostęp.

W *HelloWorldController.cs*, zmień `Welcome` metodę, aby `Message` `NumTimes` dodać i `ViewData` wartość do słownika. Słownik `ViewData` jest obiektem dynamicznym, co oznacza, że można użyć dowolnego typu; `ViewData` obiekt nie ma zdefiniowanych właściwości, dopóki nie umieścisz w nim czegoś. [System wiązania modelu MVC](xref:mvc/models/model-binding) automatycznie mapuje`name` `numTimes`nazwane parametry ( i ) z ciągu zapytania na pasku adresu do parametrów w metodzie. Kompletny plik *HelloWorldController.cs* wygląda następująco:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Obiekt `ViewData` słownika zawiera dane, które zostaną przekazane do widoku.

Utwórz szablon widoku powitalnego o nazwie *Widoki/HelloWorld/Welcome.cshtml*.

Utworzysz pętlę w szablonie widoku *Welcome.cshtml* `NumTimes`z wyświetlonym programem "Hello". Zastąp zawartość *Views/HelloWorld/Welcome.cshtml* następującymi:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Zapisz zmiany i przejdź do następującego adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Dane są pobierane z adresu URL i przekazywane do kontrolera przy użyciu [spinacza modelu MVC](xref:mvc/models/model-binding) . Kontroler pakiety danych `ViewData` do słownika i przekazuje ten obiekt do widoku. Widok następnie renderuje dane w formacie HTML do przeglądarki.

![Widok prywatności przedstawiający etykietę powitalną i wyrażenie Hello Rick pokazane cztery razy](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

W powyższym przykładzie `ViewData` słownik został użyty do przekazywania danych z kontrolera do widoku. W dalszej części samouczka model widoku jest używany do przekazywania danych z kontrolera do widoku. Podejście modelu widoku do przekazywania danych jest `ViewData` ogólnie znacznie preferowane w stosunku do podejścia słownika. Aby uzyskać więcej [informacji, zobacz Kiedy używać viewbag, ViewData lub TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)

W następnym samouczku tworzona jest baza danych filmów.

> [!div class="step-by-step"]
> [Poprzedni](adding-controller.md)
> [następny](adding-model.md)

::: moniker-end
