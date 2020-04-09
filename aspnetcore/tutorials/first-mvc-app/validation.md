---
title: Dodawanie sprawdzania poprawności do aplikacji core mvc ASP.NET
author: rick-anderson
description: Jak dodać sprawdzanie poprawności do aplikacji ASP.NET Core.
ms.author: riande
ms.date: 04/13/2017
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: ecf3d011b38347eb32020df00e44d93ca789443a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242539"
---
# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>Dodawanie sprawdzania poprawności do aplikacji core mvc ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji:

* Logika sprawdzania poprawności `Movie` jest dodawany do modelu.
* Upewnij się, że reguły sprawdzania poprawności są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.

## <a name="keeping-things-dry"></a>Utrzymywanie suchości

Jednym z założeń projektowych MVC jest [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("Don't Repeat Yourself"). ASP.NET Core MVC zachęca do określenia funkcjonalności lub zachowania tylko raz, a następnie mieć to odzwierciedlenie wszędzie w aplikacji. Zmniejsza to ilość kodu, który należy napisać i sprawia, że kod, który nie pisać mniej podatne na błędy, łatwiejsze do przetestowania i łatwiejsze do utrzymania.

Obsługa walidacji świadczona przez MVC i Entity Framework Core Code First jest dobrym przykładem zasady DRY w działaniu. Można deklaratywnie określić reguły sprawdzania poprawności w jednym miejscu (w klasie modelu), a reguły są wymuszane wszędzie w aplikacji.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Interfejs użytkownika błędu sprawdzania poprawności

Uruchom aplikację i przejdź do kontrolera Filmy.

Naciśnij **łącze Utwórz nowy,** aby dodać nowy film. Wypełnij formularz z nieprawidłowymi wartościami. Jak tylko weryfikacja po stronie klienta jQuery wykryje błąd, wyświetla komunikat o błędzie.

![Formularz widoku filmu z wieloma błędami sprawdzania poprawności po stronie klienta jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Zwróć uwagę, jak formularz automatycznie renderował odpowiedni komunikat o błędzie sprawdzania poprawności w każdym polu zawierającym nieprawidłową wartość. Błędy są wymuszane zarówno po stronie klienta (przy użyciu JavaScript i jQuery) i po stronie serwera (w przypadku, gdy użytkownik ma wyłączony JavaScript).

Istotną korzyścią jest to, że nie trzeba zmieniać `MoviesController` pojedynczy wiersz kodu w klasie lub w *Create.cshtml* widoku, aby włączyć ten interfejs użytkownika sprawdzania poprawności. Kontroler i widoki utworzone wcześniej w tym samouczku automatycznie podniósł reguły sprawdzania poprawności, które `Movie` zostały określone przy użyciu atrybutów sprawdzania poprawności właściwości klasy modelu. Sprawdź test `Edit` przy użyciu metody akcji i stosuje się tę samą weryfikację.

Dane formularza nie są wysyłane do serwera, dopóki nie ma żadnych błędów sprawdzania poprawności po stronie klienta. Można to sprawdzić, umieszczając punkt `HTTP Post` przerwania w metodzie, używając [narzędzia Fiddler](https://www.telerik.com/fiddler) lub [narzędzia F12 Developer.](/microsoft-edge/devtools-guide)

## <a name="how-validation-works"></a>Jak działa sprawdzanie poprawności

Można się zastanawiać, jak interfejs użytkownika sprawdzania poprawności został wygenerowany bez żadnych aktualizacji kodu w kontrolerze lub widokach. Poniższy kod przedstawia `Create` dwie metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

Pierwsza metoda akcji `Create` (HTTP GET) wyświetla początkowy formularz Utwórz. Druga wersja`[HttpPost]`( ) obsługuje wpis formularza. Druga `Create` metoda (Wersja) `[HttpPost]` `ModelState.IsValid` wywołuje, aby sprawdzić, czy film ma żadnych błędów sprawdzania poprawności. Wywołanie tej metody ocenia wszystkie atrybuty sprawdzania poprawności, które zostały zastosowane do obiektu. Jeśli obiekt ma błędy sprawdzania `Create` poprawności, metoda ponownie wyświetla formularz. Jeśli nie ma żadnych błędów, metoda zapisuje nowy film w bazie danych. W naszym przykładzie filmu formularz nie jest księgowany na serwerze, gdy po stronie klienta wykryto błędy sprawdzania poprawności; druga `Create` metoda nigdy nie jest wywoływana, gdy występują błędy sprawdzania poprawności po stronie klienta. Jeśli wyłączysz javascript w przeglądarce, sprawdzanie poprawności klienta `Create` `ModelState.IsValid` jest wyłączone i można przetestować metodę HTTP POST wykrywającą błędy sprawdzania poprawności.

Można ustawić punkt przerwania `[HttpPost] Create` w metodzie i sprawdzić, czy metoda nigdy nie jest wywoływana, sprawdzanie poprawności po stronie klienta nie będzie przesyłać danych formularza po wykryciu błędów sprawdzania poprawności. Jeśli wyłączysz JavaScript w przeglądarce, a następnie prześlij formularz z błędami, punkt przerwania zostanie trafiony. Nadal masz pełną walidację bez JavaScript. 

Na poniższej ilustracji pokazano, jak wyłączyć obsługę JavaScript w przeglądarce Firefox.

![Firefox: na karcie Zawartość opcji wyewidencjonuj pole wyboru Włącz javascript.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Na poniższej ilustracji pokazano, jak wyłączyć obsługę JavaScript w przeglądarce Chrome.

![Google Chrome: w sekcji Javascript w ustawieniach zawartości wybierz pozycję Nie zezwalaj żadnej witrynie na uruchamianie javascriptu.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Po wyłączeniu języka JavaScript opublikuj nieprawidłowe dane i przejmij debuger.

![Podczas debugowania na wpis nieprawidłowych danych, Intellisense na ModelState.IsValid pokazuje wartość jest false.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Część szablonu widoku *Create.cshtml* jest wyświetlana w następujących znacznikach:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Powyższe znaczniki są używane przez metody akcji do wyświetlania formularza początkowego i ponownego wyświetlania go w przypadku błędu.

[Pomocnik znacznika wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML potrzebne do sprawdzania poprawności jQuery po stronie klienta. [Pomocnik tagu sprawdzania poprawności](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy sprawdzania poprawności. Zobacz [Sprawdzanie poprawności,](xref:mvc/models/validation) aby uzyskać więcej informacji.

Co jest naprawdę miłe w tym podejściu jest `Create` to, że ani kontroler, ani szablon widoku nie wie nic o rzeczywistych reguł sprawdzania poprawności są wymuszane lub o określonych komunikatów o błędach wyświetlanych. Reguły sprawdzania poprawności i ciągi błędów są określone tylko w `Movie` klasie. Te same reguły sprawdzania poprawności `Edit` są automatycznie stosowane do widoku i innych szablonów widoków, które można utworzyć, które edytują model.

Gdy trzeba zmienić logikę sprawdzania poprawności, można to zrobić w dokładnie jednym miejscu, `Movie` dodając atrybuty sprawdzania poprawności do modelu (w tym przykładzie klasy). Nie musisz się martwić o różne części aplikacji są niezgodne z jak reguły są wymuszane — wszystkie logiki sprawdzania poprawności zostaną zdefiniowane w jednym miejscu i używane wszędzie. Dzięki temu kod jest bardzo czysty i ułatwia jego utrzymanie i ewolucję. A to oznacza, że będziesz w pełni przestrzegać zasady DRY.

## <a name="using-datatype-attributes"></a>Korzystanie z atrybutów datatype

Otwórz plik *Movie.cs* i sprawdź `Movie` klasę. Obszar `System.ComponentModel.DataAnnotations` nazw udostępnia atrybuty formatowania oprócz wbudowanego zestawu atrybutów sprawdzania poprawności. Zastosowaliśmy już wartość `DataType` wyliczenia do daty wydania i do pól cenowych. Poniższy kod `ReleaseDate` przedstawia `Price` właściwości i `DataType` z odpowiednim atrybutem.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atrybuty `DataType` zawierają tylko wskazówki dla aparatu widoku, aby sformatować `<a>` dane (i `<a href="mailto:EmailAddress.com">` dostarcza elementy/atrybuty, takie jak adres URL i dla poczty e-mail. Za pomocą `RegularExpression` atrybutu można sprawdzić poprawność formatu danych. Atrybut `DataType` jest używany do określenia typu danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych, nie są one atrybuty sprawdzania poprawności. W tym przypadku chcemy tylko śledzić datę, a nie godzinę. Wyliczenie `DataType` zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress i więcej. Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu. Na przykład `mailto:` można utworzyć łącze dla `DataType.EmailAddress`, a selektor `DataType.Date` daty może być podany w przeglądarkach obsługujących HTML5. Atrybuty `DataType` emitują `data-` atrybuty HTML 5 (wymawiane rozdzielenie danych), które przeglądarki HTML 5 mogą zrozumieć. Atrybuty `DataType` **nie** zapewniają sprawdzania poprawności.

`DataType.Date`nie określa formatu daty, która jest wyświetlana. Domyślnie pole danych jest wyświetlane zgodnie z domyślnymi formatami `CultureInfo`opartymi na serwerze .

Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być również stosowane, gdy wartość jest wyświetlana w polu tekstowym do edycji. (W przypadku niektórych pól może nie być odpowiedni symbol waluty do edycji).

Możesz użyć `DisplayFormat` tego atrybutu samodzielnie, ale zazwyczaj warto użyć tego `DataType` atrybutu. Atrybut `DataType` przekazuje semantykę danych, w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, które nie są uchybienia z DisplayFormat:

* Przeglądarka może włączyć funkcje HTML5 (na przykład, aby wyświetlić formant kalendarza, symbol waluty odpowiednie dla ustawień regionalnych, linki e-mail, itp.)

* Domyślnie przeglądarka będzie renderować dane przy użyciu odpowiedniego formatu na podstawie ustawień regionalnych.

* Atrybut `DataType` może włączyć MVC, aby wybrać odpowiedni szablon pola `DisplayFormat` do renderowania danych (jeśli używany przez siebie używa szablonu ciągu).

> [!NOTE]
> sprawdzanie poprawności jQuery nie `Range` działa z `DateTime`atrybutem i . Na przykład następujący kod zawsze będzie wyświetlać błąd sprawdzania poprawności po stronie klienta, nawet jeśli data znajduje się w określonym zakresie:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Aby użyć atrybutu z `Range` `DateTime`programem . Zazwyczaj nie jest dobrą praktyką do kompilowania twardych dat `Range` w `DateTime` modelach, więc przy użyciu atrybutu i jest odradzane.

Poniższy kod pokazuje łączenie atrybutów w jednym wierszu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

W następnej części serii przejrzymy aplikację i wypracujemy `Details` `Delete` automatycznie generowane metody.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Praca z formularzami](xref:mvc/views/working-with-forms)
* [Globalizacja i lokalizacja](xref:fundamentals/localization)
* [Wprowadzenie do pomocników tagów](xref:mvc/views/tag-helpers/intro)
* [Pomocnicy tagów autora](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Poprzedni](new-field.md)
> [następny](details.md)  
