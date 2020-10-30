---
title: Część 9, Dodawanie walidacji do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 9 serii samouczków w ASP.NET Core MVC.
ms.author: riande
ms.date: 04/13/2017
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
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 340a66c4a561c6e00bf6f38bcf51abc795aa649c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059094"
---
# <a name="part-9-add-validation-to-an-aspnet-core-mvc-app"></a>Część 9, Dodawanie walidacji do aplikacji ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji:

* Logika walidacji jest dodawana do `Movie` modelu.
* Upewnij się, że reguły walidacji są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.

## <a name="keeping-things-dry"></a>Przechowywanie SUCHEj zawartości

Jeden z założenia projektu MVC jest [suchy](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("nie powtarzaj się"). ASP.NET Core MVC zachęca do określania funkcjonalności lub zachowania tylko raz, a następnie znajdować się w dowolnym miejscu w aplikacji. Pozwala to zmniejszyć ilość kodu, który trzeba napisać, i sprawia, że kod jest mniej podatny na błędy, łatwiejszy do testowania i łatwiejszy w obsłudze.

Obsługa walidacji świadczona przez MVC i Entity Framework Core Code First jest dobrym przykładem zasady SUCHa w działaniu. Można deklaratywnie określić reguły walidacji w jednym miejscu (w klasie modelu), a reguły są wymuszane wszędzie w aplikacji.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Interfejs użytkownika błędu walidacji

Uruchom aplikację i przejdź do kontrolera filmów.

Naciśnij link **Utwórz nowy** , aby dodać nowy film. Wypełnij formularz nieprawidłowymi wartościami. Gdy tylko Walidacja po stronie klienta jQuery wykryje błąd, zostanie wyświetlony komunikat o błędzie.

![Formularz wyświetlania filmu z wieloma błędami walidacji po stronie klienta jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Zwróć uwagę, jak formularz automatycznie renderuje odpowiedni komunikat o błędzie walidacji w każdym polu zawierającym nieprawidłową wartość. Błędy są wymuszane po stronie klienta (przy użyciu języków JavaScript i jQuery) i po stronie serwera (w przypadku, gdy użytkownik ma wyłączony kod JavaScript).

Znacząca korzyść polega na tym, że nie trzeba zmieniać pojedynczego wiersza kodu w `MoviesController` klasie lub w widoku *Create. cshtml* , aby włączyć ten interfejs użytkownika weryfikacji. Kontroler i widoki utworzone wcześniej w tym samouczku automatycznie pobierają reguły sprawdzania poprawności określone przy użyciu atrybutów walidacji we właściwościach `Movie` klasy modelu. Sprawdzanie poprawności testu przy użyciu `Edit` metody akcji i zastosowanie tej samej walidacji.

Dane formularza nie są wysyłane do serwera, dopóki nie zostaną wykryte błędy weryfikacji po stronie klienta. Można to sprawdzić, umieszczając punkt przerwania w `HTTP Post` metodzie przy użyciu [Narzędzia programu Fiddler](https://www.telerik.com/fiddler) lub [narzędzi programistycznych F12](/microsoft-edge/devtools-guide).

## <a name="how-validation-works"></a>Jak działa Walidacja

Możesz zastanawiać się, jak został wygenerowany interfejs użytkownika weryfikacji bez aktualizacji kodu w kontrolerze lub widokach. Poniższy kod przedstawia dwie `Create` metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

Pierwsza metoda akcji (HTTP GET) `Create` zawiera początkowy formularz tworzenia. Druga wersja ( `[HttpPost]` ) obsługuje wpis w formularzu. Druga `Create` Metoda ( `[HttpPost]` wersja) wywołania, `ModelState.IsValid` Aby sprawdzić, czy film ma jakiekolwiek błędy walidacji. Wywołanie tej metody szacuje wszystkie atrybuty walidacji, które zostały zastosowane do obiektu. Jeśli obiekt ma błędy walidacji, `Create` Metoda ponowna wyświetli formularz. Jeśli nie ma żadnych błędów, Metoda zapisuje nowy film w bazie danych. W naszym przykładzie filmu nie jest on ogłaszany na serwerze, gdy na stronie klienta wykryto błędy walidacji. Druga `Create` Metoda nie jest nigdy wywoływana, gdy występują błędy walidacji po stronie klienta. W przypadku wyłączenia języka JavaScript w przeglądarce sprawdzanie poprawności klienta jest wyłączone i można przetestować metodę POST protokołu HTTP w celu `Create` `ModelState.IsValid` wykrycia błędów walidacji.

Można ustawić punkt przerwania w `[HttpPost] Create` metodzie i sprawdzić, czy metoda nie jest nigdy wywoływana, podczas walidacji po stronie klienta nie będą przesyłane dane formularza po wykryciu błędów walidacji. Jeśli wyłączysz JavaScript w przeglądarce, a następnie prześlesz formularz z błędami, zostanie osiągnięty punkt przerwania. Nadal będziesz mieć pełną weryfikację bez języka JavaScript. 

Na poniższej ilustracji przedstawiono sposób wyłączania języka JavaScript w przeglądarce Firefox.

![Firefox: na karcie zawartość w obszarze Opcje usuń zaznaczenie pola wyboru Włącz język JavaScript.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Na poniższej ilustracji przedstawiono sposób wyłączania języka JavaScript w przeglądarce Chrome.

![Google Chrome: w sekcji JavaScript ustawień zawartości wybierz opcję nie Zezwalaj na uruchamianie skryptów JavaScript przez żadną lokację.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Po wyłączeniu języka JavaScript Opublikuj nieprawidłowe dane i przechodzenie przez debuger.

![Podczas debugowania wpisu nieprawidłowych danych funkcja IntelliSense w ModelState. IsValid pokazuje wartość false.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Część szablonu widoku *Create. cshtml* jest pokazana w następującej postaci:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Poprzedzające znaczniki są używane przez metody akcji, aby wyświetlić początkowy formularz i ponownie wyświetlić go w przypadku błędu.

[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta. [Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy walidacji. Aby uzyskać więcej informacji, zobacz [Walidacja](xref:mvc/models/validation) .

W rzeczywistości to podejście jest takie, że ani kontroler, ani `Create` szablon widoku nie wie o faktycznych regułach walidacji lub o określonych komunikatach o błędach. Reguły walidacji i ciągi błędów są określone tylko w `Movie` klasie. Te same reguły sprawdzania poprawności są automatycznie stosowane do `Edit` widoku i wszystkich innych szablonów widoków, które można utworzyć, edytując model.

W przypadku konieczności zmiany logiki walidacji można to zrobić w dokładnie jednym miejscu przez dodanie atrybutów sprawdzania poprawności do modelu (w tym przykładzie `Movie` Klasa). Nie trzeba martwić się o różne części aplikacji, które nie są zgodne z zasadami, w których są wymuszane — Cała logika walidacji zostanie zdefiniowana w jednym miejscu i użyta wszędzie. Dzięki temu kod jest bardzo czysty i ułatwia utrzymanie i rozwój. Oznacza to, że będziesz w pełni przestrzegać SUCHEj zasady.

## <a name="using-datatype-attributes"></a>Używanie atrybutów DataType

Otwórz plik *Movie.cs* i zapoznaj się z `Movie` klasą. `System.ComponentModel.DataAnnotations`Przestrzeń nazw zawiera atrybuty formatowania oprócz wbudowanego zestawu atrybutów walidacji. Wartość wyliczenia została już zastosowana `DataType` do daty wydania i do pól cen. Poniższy kod pokazuje `ReleaseDate` `Price` właściwości i z odpowiednim `DataType` atrybutem.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType`Atrybuty zawierają tylko wskazówki dla aparatu widoku do formatowania danych (i udostępniają elementy/atrybuty, takie jak `<a>` adresy URL i `<a href="mailto:EmailAddress.com">` wiadomości e-mail. Możesz użyć atrybutu, `RegularExpression` Aby sprawdzić poprawność formatu danych. Ten `DataType` atrybut służy do określania typu danych, który jest bardziej szczegółowy niż typ wewnętrzny bazy danych, nie są atrybutami walidacji. W tym przypadku chcemy tylko śledzić datę, a nie godzinę. `DataType`Wyliczenie zawiera wiele typów danych, takich jak data, godzina, numer telefonu, waluta, EmailAddress i inne. Ten `DataType` atrybut może również umożliwić aplikacji automatyczne udostępnianie funkcji specyficznych dla typu. Na przykład `mailto:` można utworzyć łącze dla i dla programu `DataType.EmailAddress` `DataType.Date` w przeglądarkach, które obsługują HTML5, można podać selektor daty. `DataType`Atrybuty emitują HTML 5 `data-` (wymawiane kreski danych), które mogą zrozumieć przeglądarki HTML 5. `DataType`Atrybuty nie zapewniają **not** żadnej weryfikacji.

`DataType.Date` nie określa formatu wyświetlanej daty. Domyślnie pole dane jest wyświetlane zgodnie z domyślnymi formatami opartymi na serwerze `CultureInfo` .

Ten `DisplayFormat` atrybut służy do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode`Ustawienie określa, że formatowanie powinno być również stosowane, gdy wartość jest wyświetlana w polu tekstowym do edycji. (Możesz nie chcieć, aby w przypadku niektórych pól — na przykład w przypadku wartości walutowych, prawdopodobnie nie chcesz, aby symbol waluty był widoczny w polu tekstowym do edycji).

Możesz użyć `DisplayFormat` atrybutu przez siebie, ale zazwyczaj dobrym pomysłem jest użycie `DataType` atrybutu. Ten `DataType` atrybut przekazuje semantykę danych w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, których nie można uzyskać za pomocą DisplayFormat:

* Przeglądarka może włączać funkcje HTML5 (na przykład w celu wyświetlania kontrolki kalendarza, symbolu waluty właściwej dla ustawień regionalnych, linków e-mail itp.).

* Domyślnie przeglądarka będzie renderować dane przy użyciu poprawnego formatu na podstawie ustawień regionalnych.

* Ten `DataType` atrybut umożliwia wybranie odpowiedniego szablonu pola do renderowania danych `DisplayFormat` przez MVC. (Jeśli używane przez siebie korzysta z szablonu ciągu).

> [!NOTE]
> Walidacja jQuery nie działa z `Range` atrybutem i `DateTime` . Na przykład poniższy kod zawsze będzie wyświetlał błąd walidacji po stronie klienta, nawet wtedy, gdy data jest w określonym zakresie:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Należy wyłączyć sprawdzanie poprawności daty jQuery, aby użyć `Range` atrybutu z `DateTime` . Ogólnie rzecz biorąc, nie jest dobrym sposobem kompilowania dat stałych w modelach, dlatego przy użyciu `Range` atrybutu i `DateTime` nie jest to odradzane.

Poniższy kod ilustruje łączenie atrybutów w jednym wierszu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

W następnej części serii przeglądamy aplikację i wprowadzamy pewne ulepszenia dla automatycznie generowanych `Details` i `Delete` metod.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Praca z formularzami](xref:mvc/views/working-with-forms)
* [Globalizacja i lokalizacja](xref:fundamentals/localization)
* [Wprowadzenie do pomocy tagów](xref:mvc/views/tag-helpers/intro)
* [Autorzy tagów](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Poprzedni](new-field.md) 
>  [Dalej](details.md)  
