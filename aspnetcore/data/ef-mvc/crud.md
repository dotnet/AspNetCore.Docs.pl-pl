---
title: 'Samouczek: Implementowanie funkcji CRUD - ASP.NET MVC z EF Core'
description: W tym samouczku przejrzysz i dostosujesz kod CRUD (tworzenie, odczyt, aktualizacja, usuwanie), który szkieletowanie MVC automatycznie tworzy dla Ciebie w kontrolerach i widokach.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/crud
ms.openlocfilehash: 2aa4ef48509b9a34f3b25eb657b1ecac51c1374b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416213"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Samouczek: Implementowanie funkcji CRUD - ASP.NET MVC z EF Core

W poprzednim samouczku utworzono aplikację MVC, która przechowuje i wyświetla dane przy użyciu entity framework i SQL Server LocalDB. W tym samouczku przejrzysz i dostosujesz kod CRUD (tworzenie, odczyt, aktualizacja, usuwanie), który szkieletowanie MVC automatycznie tworzy dla Ciebie w kontrolerach i widokach.

> [!NOTE]
> Jest powszechną praktyką, aby zaimplementować wzorzec repozytorium w celu utworzenia warstwy abstrakcji między kontrolerem a warstwą dostępu do danych. Aby te samouczki były proste i skoncentrowane na nauczaniu, jak korzystać z samej struktury encji, nie używają repozytoriów. Aby uzyskać informacje na temat repozytoriów z EF, zobacz [ostatni samouczek z tej serii](advanced.md).

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dostosowywanie strony Szczegóły
> * Aktualizowanie strony Tworzenie
> * Aktualizowanie strony edycji
> * Aktualizowanie strony Usuwanie
> * Zamykanie połączeń z bazą danych

## <a name="prerequisites"></a>Wymagania wstępne

* [Wprowadzenie do EF Core i ASP.NET Core MVC](intro.md)

## <a name="customize-the-details-page"></a>Dostosowywanie strony Szczegóły

Szkieletowy kod dla students index strony pominął `Enrollments` właściwość, ponieważ ta właściwość posiada kolekcję. Na stronie **Szczegóły** zawartość kolekcji zostanie wyświetlona w tabeli HTML.

W *Controllers/StudentsController.cs*metoda akcji dla widoku Szczegóły `SingleOrDefaultAsync` używa metody do `Student` pobierania pojedynczej jednostki. Dodaj kod, `Include`który wywołuje . `ThenInclude`i `AsNoTracking` metody, jak pokazano w poniższym wyróżnionym kodzie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

I `Include` `ThenInclude` metody spowodować kontekst załadować właściwość `Student.Enrollments` nawigacji i `Enrollment.Course` w ramach każdej rejestracji właściwości nawigacji.  Dowiesz się więcej o tych metodach w samouczku [odczytu powiązanych danych.](read-related-data.md)

Metoda `AsNoTracking` zwiększa wydajność w scenariuszach, w których zwrócone jednostki nie będą aktualizowane w okresie istnienia bieżącego kontekstu. Dowiesz się więcej `AsNoTracking` na temat na końcu tego samouczka.

### <a name="route-data"></a>Dane trasy

Wartość klucza przekazywana `Details` do metody pochodzi z *danych trasy*. Dane trasy to dane znalezione przez spinacz modelu w segmencie adresu URL. Na przykład trasa domyślna określa segmenty kontrolera, akcji i identyfikatora:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

W poniższym adresie URL domyślna trasa mapuje instruktora jako kontrolera, Indeks jako akcję i 1 jako identyfikator; są to wartości danych trasy.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

Ostatnia część adresu URL ("?courseID=2021") jest wartością ciągu zapytania. Spinacza modelu przekaże również wartość `Index` identyfikatora do parametru metody, `id` jeśli przekażesz go jako wartość ciągu zapytania:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Na stronie Indeks adresy URL hiperłączy są tworzone przez instrukcje pomocnika znaczników w widoku Razor. W poniższym kodzie `id` Razor parametr jest `id` zgodny z trasą domyślną, więc jest dodawany do danych trasy.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Spowoduje to wygenerowanie `item.ID` następującego kodu HTML w przypadku 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

W poniższym kodzie `studentID` Razor nie pasuje do parametru w trasie domyślnej, więc jest dodawany jako ciąg zapytania.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Spowoduje to wygenerowanie `item.ID` następującego kodu HTML w przypadku 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Aby uzyskać więcej informacji na <xref:mvc/views/tag-helpers/intro>temat pomocników tagów, zobacz .

### <a name="add-enrollments-to-the-details-view"></a>Dodawanie rejestracji do widoku Szczegóły

Otwórz *widoki/Studenci/Szczegóły.cshtml*. Każde pole jest `DisplayNameFor` wyświetlane `DisplayFor` przy użyciu i pomocników, jak pokazano w poniższym przykładzie:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Po ostatnim polu i bezpośrednio `</dl>` przed tagiem zamknięcia dodaj następujący kod, aby wyświetlić listę rejestracji:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Jeśli wcięcie kodu jest nieprawidłowe po wklejeniu kodu, naciśnij klawisz CTRL-K-D, aby go poprawić.

Ten kod pętli za pośrednictwem `Enrollments` jednostek we właściwości nawigacji. Dla każdej rejestracji wyświetla tytuł kursu i klasę. Tytuł kursu jest pobierany z Course jednostki, `Course` która jest przechowywana we właściwości nawigacji enrollments jednostki.

Uruchom aplikację, wybierz kartę **Uczniowie** i kliknij **łącze Szczegóły** dla ucznia. Zobaczysz listę kursów i ocen dla wybranego ucznia:

![Strona Szczegóły ucznia](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Aktualizowanie strony Tworzenie

W *StudentsController.cs*, zmodyfikuj metodę HttpPost, `Create` dodając blok try-catch i usuwając identyfikator z atrybutu. `Bind`

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Ten kod dodaje Student jednostki utworzone przez ASP.NET Core Model Spinacz do zestawu jednostek Studenci, a następnie zapisuje zmiany w bazie danych. (Spinacz modelu odnosi się do funkcji ASP.NET Core MVC, która ułatwia pracę z danymi przesłanych przez formularz; spinacz modelu konwertuje zaksięgowane wartości formularzy na typy CLR i przekazuje je do metody akcji w parametrach. W takim przypadku spinacz modelu tworzy wystąpienia jednostki Student dla Ciebie przy użyciu wartości właściwości z kolekcji Form.)

Usunięto `ID` z atrybutu, `Bind` ponieważ identyfikator jest wartością klucza podstawowego, którą sql server ustawi automatycznie po wstawieniu wiersza. Dane wejściowe od użytkownika nie ustawia wartość identyfikatora.

Inne niż `Bind` atrybut, try-catch bloku jest jedyną zmianą, którą dokonano do szkieletu kodu. Jeśli wyjątek, który `DbUpdateException` pochodzi z jest przechwytywany podczas zapisywania zmian, wyświetlany jest ogólny komunikat o błędzie. `DbUpdateException`wyjątki są czasami spowodowane przez coś zewnętrznego do aplikacji, a nie błąd programowania, więc użytkownik powinien spróbować ponownie. Chociaż nie zostały zaimplementowane w tym przykładzie, aplikacja jakości produkcji będzie rejestrować wyjątek. Aby uzyskać więcej informacji, zobacz **sekcję Dziennik dla wglądu** w [monitorowanie i telemetrię (Tworzenie rzeczywistych aplikacji w chmurze za pomocą platformy Azure).](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)

Atrybut `ValidateAntiForgeryToken` pomaga zapobiegać atakom fałszerstwa żądań między witrynami (CSRF). Token jest automatycznie wstrzykiwany do widoku przez [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) i jest uwzględniany podczas przesyłania formularza przez użytkownika. Token jest sprawdzany `ValidateAntiForgeryToken` przez atrybut. Aby uzyskać więcej informacji na temat CSRF, zobacz [Forgery przeciw żądaniem](../../security/anti-request-forgery.md).

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Uwaga dotycząca zabezpieczeń dotycząca nadpostywania

Atrybut, `Bind` który szkieletowidek zawiera `Create` w metodzie jest jednym ze sposobów ochrony przed overposting w scenariuszach tworzenia. Załóżmy na przykład, `Secret` że jednostka Student zawiera właściwość, której nie chcesz ustawiać tej strony sieci Web.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

Nawet jeśli nie masz `Secret` pola na stronie internetowej, haker może użyć narzędzia, takiego jak Fiddler, `Secret` lub napisać javascript, aby opublikować wartość formularza. Bez `Bind` atrybutu ograniczającego pola używane przez spinacza modelu podczas tworzenia wystąpienia studenta, `Secret` spinacz modelu będzie odebrać tę wartość formularza i użyć go do utworzenia wystąpienia jednostki Student. Następnie niezależnie od wartości hakera określonego dla pola `Secret` formularza zostaną zaktualizowane w bazie danych. Na poniższej ilustracji przedstawiono `Secret` narzędzie Fiddler dodające pole (z wartością "OverPost") do zaksięgowanych wartości formularza.

![Skrzypek dodający pole Tajne](crud/_static/fiddler.png)

Wartość "OverPost" zostanie następnie pomyślnie `Secret` dodana do właściwości wstawionego wiersza, chociaż nigdy nie zamierzałeś, aby strona sieci web mogła ustawić tę właściwość.

Można zapobiec overposting w scenariuszach edycji, najpierw odczytując `TryUpdateModel`jednostkę z bazy danych, a następnie wywołując , przekazując na liście jawnych dozwolonych właściwości. Jest to metoda używana w tych samouczkach.

Alternatywnym sposobem zapobiegania overposting, który jest preferowany przez wielu deweloperów jest użycie modeli widoku, a nie klasy jednostek z powiązaniem modelu. Uwzględnij tylko właściwości, które chcesz zaktualizować w modelu widoku. Po zakończeniu spinacza modelu MVC skopiuj właściwości modelu widoku do wystąpienia jednostki, opcjonalnie używając narzędzia, takiego jak AutoMapper. Użyj `_context.Entry` w wystąpieniu jednostki, `Unchanged`aby ustawić `Property("PropertyName").IsModified` jego stan na , a następnie ustawić wartość true dla każdej właściwości jednostki, która jest uwzględniona w modelu widoku. Ta metoda działa zarówno w edytować i tworzyć scenariusze.

### <a name="test-the-create-page"></a>Testowanie strony Tworzenie

Kod w *widokach/uczniach/create.cshtml* używa `label`, `input`i `span` (dla wiadomości sprawdzania poprawności) pomocników tagów dla każdego pola.

Uruchom aplikację, wybierz kartę **Uczniowie** i kliknij pozycję **Utwórz nowy**.

Wprowadź nazwy i datę. Spróbuj wprowadzić nieprawidłową datę, jeśli przeglądarka ci na to pozwala. (Niektóre przeglądarki zmuszają do używania selektora dat). Następnie kliknij przycisk **Utwórz,** aby wyświetlić komunikat o błędzie.

![Błąd sprawdzania poprawności daty](crud/_static/date-error.png)

Jest to sprawdzanie poprawności po stronie serwera, które są domyślnie ujmowany; w późniejszym samouczku zobaczysz, jak dodać atrybuty, które wygenerują kod do sprawdzania poprawności po stronie klienta. Poniższy wyróżniony kod przedstawia sprawdzanie `Create` poprawności modelu w metodzie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Zmień datę na prawidłową wartość i kliknij przycisk **Utwórz,** aby nowy uczeń pojawił się na stronie **Indeks.**

## <a name="update-the-edit-page"></a>Aktualizowanie strony edycji

W *StudentController.cs*, HttpGet `Edit` metoda (jeden bez `HttpPost` atrybutu) używa `SingleOrDefaultAsync` metody do pobierania wybranej jednostki Student, jak widać w `Details` metodzie. Nie trzeba zmieniać tej metody.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Zalecany kod edycji HttpPost: Czytaj i aktualizuj

Zastąp metodę akcji Edycja HttpPost następującym kodem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Te zmiany implementują najlepsze rozwiązania w zakresie zabezpieczeń, aby zapobiec nadpostowywania. Rusztowania generowane `Bind` atrybut i dodał jednostki utworzone przez spinacza modelu `Modified` do jednostki zestaw z flagą. Ten kod nie jest zalecane w `Bind` wielu scenariuszach, ponieważ atrybut czyści wszystkie istniejące `Include` dane w polach nie wymienionych w parametrze.

Nowy kod odczytuje istniejącą `TryUpdateModel` encję i wywołuje zaktualizowanie pól w pobranej encji [na podstawie danych wejściowych użytkownika w opublikowanych danych formularza](xref:mvc/models/model-binding). Funkcja automatycznego śledzenia zmian w `Modified` ramach encji ustawia flagę na polach, które są zmieniane przez dane wejściowe formularza. Gdy `SaveChanges` metoda jest wywoływana, Entity Framework tworzy instrukcje SQL, aby zaktualizować wiersz bazy danych. Konflikty współbieżności są ignorowane i tylko kolumny tabel, które zostały zaktualizowane przez użytkownika są aktualizowane w bazie danych. (W późniejszym samouczku pokazano, jak obsługiwać konflikty współbieżności.

Najlepszym rozwiązaniem, aby zapobiec nadpostowaniu, pola, które mają być aktualizowane przez **edit** strony są umieszczane na białej `TryUpdateModel` liście w parametrach. (Pusty ciąg poprzedzający listę pól na liście parametrów jest dla prefiksu do użycia z nazwami pól formularza). Obecnie nie ma żadnych dodatkowych pól, które chronisz, ale lista pól, które mają być powiązane spinacza modelu, gwarantuje, że jeśli dodasz pola do modelu danych w przyszłości, są one automatycznie chronione, dopóki nie zostanie jawnie dodane tutaj.

W wyniku tych zmian podpis metody HttpPost `Edit` metody jest taka sama `Edit` jak HttpGet metody; w związku z tym zmieniono nazwę metody `EditPost`.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Alternatywny kod edycji HttpPost: Tworzenie i dołączanie

Zalecany kod edycji HttpPost zapewnia, że tylko zmienione kolumny są aktualizowane i zachowuje dane we właściwościach, które nie mają być uwzględnione dla powiązania modelu. Jednak podejście do odczytu wymaga odczytu dodatkowej bazy danych i może spowodować bardziej złożony kod do obsługi konfliktów współbieżności. Alternatywą jest dołączenie jednostki utworzonej przez spinacza modelu do kontekstu EF i oznaczenie go jako zmodyfikowanego. (Nie aktualizuj projektu za pomocą tego kodu, jest on wyświetlany tylko w celu zilustrowania opcjonalnego podejścia).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Można użyć tego podejścia, gdy interfejs użytkownika strony sieci web zawiera wszystkie pola w jednostce i można zaktualizować dowolną z nich.

Szkieletowy kod używa metody create-and-attach, ale tylko `DbUpdateConcurrencyException` połowy wyjątków i zwraca 404 kody błędów.  Pokazany przykład łapie wyjątek aktualizacji bazy danych i wyświetla komunikat o błędzie.

### <a name="entity-states"></a>Państwa członkowskie encji

Kontekst bazy danych śledzi, czy jednostki w pamięci są zsynchronizowane z odpowiadającymi im wierszami w bazie danych, a te informacje określają, co się dzieje po wywołaniu `SaveChanges` metody. Na przykład po przełknięciu `Add` nowej jednostki do metody stan `Added`tej encji jest ustawiony na . Następnie po wywołaniu `SaveChanges` metody kontekst bazy danych wydaje polecenie SQL INSERT.

Jednostka może znajdować się w jednym z następujących stanów:

* `Added`. Jednostka jeszcze nie istnieje w bazie danych. Metoda `SaveChanges` wystawia instrukcję INSERT.

* `Unchanged`. Metoda nie musi być wykonywana `SaveChanges` z tą jednostką za pomocą metody. Podczas odczytywania jednostki z bazy danych, jednostka rozpoczyna się od tego stanu.

* `Modified`. Niektóre lub wszystkie wartości właściwości jednostki zostały zmodyfikowane. Metoda `SaveChanges` wystawia instrukcję UPDATE.

* `Deleted`. Encja została oznaczona do usunięcia. Metoda `SaveChanges` wystawia instrukcję DELETE.

* `Detached`. Jednostka nie jest śledzona przez kontekst bazy danych.

W aplikacji klasycznej zmiany stanu są zazwyczaj ustawiane automatycznie. Odczytasz encję i wprowadzać zmiany w niektórych jej wartościach właściwości. Powoduje to, że jego stan `Modified`jednostki jest automatycznie zmieniany na . Następnie podczas `SaveChanges`wywoływania, Entity Framework generuje instrukcję SQL UPDATE, która aktualizuje tylko rzeczywiste właściwości, które zostały zmienione.

W aplikacji sieci `DbContext` web, która początkowo odczytuje jednostkę i wyświetla jej dane do edycji jest usuwany po renderowaniu strony. Gdy wywoływana `Edit` jest metoda akcji HttpPost, zostanie wykonane nowe żądanie `DbContext`sieci web i pojawi się nowe wystąpienie pliku . Jeśli ponownie odczytasz jednostkę w tym nowym kontekście, symulujesz przetwarzanie pulpitu.

Ale jeśli nie chcesz wykonać operację odczytu dodatkowego, należy użyć obiektu jednostki utworzone przez spinacza modelu.  Najprostszym sposobem, aby to zrobić, jest ustawienie stanu jednostki na Zmodyfikowany, tak jak odbywa się to w alternatywnym kodzie edycji HttpPost pokazanym wcześniej. Następnie podczas `SaveChanges`wywoływania, Entity Framework aktualizuje wszystkie kolumny wiersza bazy danych, ponieważ kontekst nie ma sposobu, aby wiedzieć, które właściwości zostały zmienione.

Jeśli chcesz uniknąć podejścia do odczytu, ale chcesz również instrukcji SQL UPDATE zaktualizować tylko pola, które użytkownik faktycznie zmienił, kod jest bardziej złożony. Musisz zapisać oryginalne wartości w jakiś sposób (na przykład za pomocą ukrytych pól), `Edit` tak aby były one dostępne, gdy wywoływana jest metoda HttpPost. Następnie można utworzyć jednostkę Student przy użyciu `Attach` oryginalnych wartości, wywołać metodę z tą oryginalną wersją encji, zaktualizować wartości encji do nowych wartości, a następnie wywołać `SaveChanges`.

### <a name="test-the-edit-page"></a>Testowanie strony Edycji

Uruchom aplikację, wybierz kartę **Uczniowie,** a następnie kliknij **hiperłącze Edycja.**

![Uczniowie edytują stronę](crud/_static/student-edit.png)

Zmień niektóre dane i kliknij przycisk **Zapisz**. Zostanie otwarta strona **Indeks** i zobaczysz zmienione dane.

## <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

W *StudentController.cs*, kod szablonu dla `Delete` HttpGet metoda `SingleOrDefaultAsync` używa metody do pobierania wybranej jednostki Student, jak widać w Szczegóły i Edytuj metody. Jednak aby zaimplementować niestandardowy `SaveChanges` komunikat o błędzie, gdy wywołanie nie powiedzie się, należy dodać niektóre funkcje do tej metody i jej odpowiedniego widoku.

Jak widać dla aktualizacji i tworzenia operacji, operacje usuwania wymagają dwóch metod akcji. Metoda, która jest wywoływana w odpowiedzi na żądanie GET wyświetla widok, który daje użytkownikowi możliwość zatwierdzenia lub anulowania operacji usuwania. Jeśli użytkownik go zatwierdzi, zostanie utworzone żądanie POST. W takim przypadku wywoływana jest metoda HttpPost, `Delete` a następnie ta metoda faktycznie wykonuje operację usuwania.

Dodasz blok try-catch do metody `Delete` HttpPost, aby obsłużyć wszelkie błędy, które mogą wystąpić podczas aktualizacji bazy danych. Jeśli wystąpi błąd, HttpPost Delete metoda wywołuje HttpGet Delete metody, przekazując go parametr, który wskazuje, że wystąpił błąd. HttpGet Delete Metoda następnie ponownie emituje stronę potwierdzenia wraz z komunikatem o błędzie, dając użytkownikowi możliwość anulowania lub spróbuj ponownie.

Zastąp `Delete` httpget metody akcji z następującym kodem, który zarządza raportowania błędów.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Ten kod akceptuje parametr opcjonalny, który wskazuje, czy metoda została wywołana po nieuchronienie zmian. Ten parametr jest false, `Delete` gdy HttpGet metoda jest wywoływana bez poprzedniego błędu. Gdy jest wywoływana przez `Delete` metodę HttpPost w odpowiedzi na błąd aktualizacji bazy danych, parametr jest true i komunikat o błędzie jest przekazywany do widoku.

### <a name="the-read-first-approach-to-httppost-delete"></a>Pierwsze podejście do httppost delete

Zastąp `Delete` metodę akcji `DeleteConfirmed`HttpPost (o nazwie) następującym kodem, który wykonuje operację usuwania rzeczywistego i wychwytuje błędy aktualizacji bazy danych.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Ten kod pobiera wybranej jednostki, a następnie wywołuje `Remove` metodę, aby ustawić stan jednostki na `Deleted`. Po `SaveChanges` wywołaniu jest generowane polecenie SQL DELETE.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>Podejście do tworzenia i dołączania do httppost delete

Jeśli priorytetem jest poprawa wydajności w aplikacji o dużej objętości, można uniknąć niepotrzebnego zapytania SQL, tworząc wystąpienie jednostki Student przy użyciu tylko wartości klucza podstawowego, a następnie ustawiając stan jednostki na `Deleted`. To wszystko, czego potrzebuje entity framework w celu usunięcia jednostki. (Nie umieszczaj tego kodu w projekcie, ale jest tutaj tylko po to, aby zilustrować alternatywę).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Jeśli jednostka ma powiązane dane, które również powinny zostać usunięte, upewnij się, że kaskadowe usuwanie jest skonfigurowane w bazie danych. Dzięki takiemu podejściu do usuwania encji EF może nie zdawać sobie sprawy, że istnieją powiązane jednostki do usunięcia.

### <a name="update-the-delete-view"></a>Aktualizowanie widoku Usuń

W *obszarze Widoki/Student/Delete.cshtml*dodaj komunikat o błędzie między nagłówkiem h2 a nagłówkiem h3, jak pokazano w poniższym przykładzie:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Uruchom aplikację, wybierz kartę **Uczniowie** i kliknij usuń **hiperłącze:**

![Usuń stronę potwierdzenia](crud/_static/student-delete.png)

Kliknij **pozycję Usuń**. Strona Indeks jest wyświetlana bez usuniętego ucznia. (Zobaczysz przykład kodu obsługi błędów w akcji w samouczku współbieżności).)

## <a name="close-database-connections"></a>Zamykanie połączeń z bazą danych

Aby zwolnić zasoby, które przechowuje połączenie z bazą danych, wystąpienie kontekstu musi zostać usunięte tak szybko, jak to możliwe, gdy skończysz z nim. Wbudowany [iniekcja zależności](../../fundamentals/dependency-injection.md) ASP.NET Core zajmuje się tym zadaniem.

W *Startup.cs*, należy wywołać [AddDbContext metody rozszerzenia](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) do inicjowania obsługi administracyjnej `DbContext` klasy w kontenerze ASP.NET Core DI. Ta metoda domyślnie `Scoped` ustawia okres istnienia usługi. `Scoped`oznacza, że okres istnienia obiektu kontekstu pokrywa `Dispose` się z czasem życia żądania sieci web, a metoda zostanie wywołana automatycznie na końcu żądania sieci web.

## <a name="handle-transactions"></a>Obsługa transakcji

Domyślnie Entity Framework niejawnie implementuje transakcje. W scenariuszach, w których można wprowadzić zmiany `SaveChanges`w wielu wierszach lub tabelach, a następnie wywołać , Entity Framework automatycznie upewnia się, że wszystkie zmiany zakończy się pomyślnie lub wszystkie one nie powiedzie się. Jeśli niektóre zmiany są wykonywane najpierw, a następnie występuje błąd, zmiany te są automatycznie przywracane. W przypadku scenariuszy, w których potrzebujesz większej kontroli — na przykład, jeśli chcesz uwzględnić operacje wykonywane poza platformą Encji w transakcji — zobacz [Transakcje](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Zapytania bez śledzenia

Gdy kontekst bazy danych pobiera wiersze tabeli i tworzy obiekty encji, które je reprezentują, domyślnie śledzi, czy jednostki w pamięci są zsynchronizowane z tym, co znajduje się w bazie danych. Dane w pamięci działa jako pamięć podręczna i jest używany podczas aktualizacji jednostki. Buforowanie to jest często niepotrzebne w aplikacji sieci web, ponieważ wystąpienia kontekstu są zazwyczaj krótkotrwałe (nowy jest tworzony i usuwany dla każdego żądania), a kontekst, który odczytuje jednostkę, jest zazwyczaj usuwany przed ponownym użyciem tej jednostki.

Można wyłączyć śledzenie obiektów jednostki w `AsNoTracking` pamięci, wywołując metodę. Typowe scenariusze, w których można to zrobić, obejmują następujące czynności:

* W okresie istnienia kontekstu nie trzeba aktualizować żadnych jednostek i nie trzeba EF [automatycznie ładować właściwości nawigacji z jednostek pobranych przez oddzielne kwerendy](read-related-data.md). Często te warunki są spełnione w metodach akcji HttpGet kontrolera.

* Uruchamiasz kwerendę, która pobiera dużą ilość danych, a tylko niewielka część zwróconych danych zostanie zaktualizowana. Może być bardziej wydajne, aby wyłączyć śledzenie dla dużych kwerendy i uruchomić kwerendę później dla kilku jednostek, które muszą zostać zaktualizowane.

* Chcesz dołączyć jednostkę w celu jej aktualizacji, ale wcześniej pobrano tę samą jednostkę w innym celu. Ponieważ jednostka jest już śledzona przez kontekst bazy danych, nie można dołączyć encji, którą chcesz zmienić. Jednym ze sposobów obsługi tej `AsNoTracking` sytuacji jest wywołanie wcześniejszej kwerendy.

Aby uzyskać więcej informacji, zobacz [Śledzenie a brak śledzenia](/ef/core/querying/tracking).

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dostosowano stronę Szczegóły
> * Zaktualizowano stronę Tworzenie
> * Zaktualizowano stronę Edycji
> * Zaktualizowano stronę Usuwanie
> * Zamknięte połączenia z bazą danych

Przejdź do następnego samouczka, aby dowiedzieć się, jak rozszerzyć funkcjonalność strony **Indeks,** dodając sortowanie, filtrowanie i stronicowanie.

> [!div class="nextstepaction"]
> [Dalej: Sortowanie, filtrowanie i stronicowanie](sort-filter-page.md)
