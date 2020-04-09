---
title: 'Samouczek: Obsługa współbieżności - ASP.NET MVC z EF Core'
description: W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje tę samą jednostkę w tym samym czasie.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 6839e383093b993ff55095f26cf88cd68708f001
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657396"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Samouczek: Obsługa współbieżności - ASP.NET MVC z EF Core

We wcześniejszych samouczkach dowiesz się, jak zaktualizować dane. W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje tę samą jednostkę w tym samym czasie.

Utworzysz strony sieci web, które współpracują z jednostką Dział i obsługują błędy współbieżności. Na poniższych ilustracjach przedstawiono strony Edytowanie i usuwanie, w tym niektóre komunikaty, które są wyświetlane w przypadku wystąpienia konfliktu współbieżności.

![Strona Edycji działu](concurrency/_static/edit-error.png)

![Strona Usuwania działów](concurrency/_static/delete-error.png)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dowiedz się więcej o konfliktach współbieżności
> * Dodawanie właściwości śledzenia
> * Tworzenie kontrolera i widoków działów
> * Aktualizuj widok indeksu
> * Aktualizuj metody edycji
> * Aktualizuj widok edycji
> * Testowanie konfliktów współbieżności
> * Aktualizowanie strony Usuwanie
> * Aktualizowanie szczegółów i tworzenie widoków

## <a name="prerequisites"></a>Wymagania wstępne

* [Aktualizowanie powiązanych danych](update-related-data.md)

## <a name="concurrency-conflicts"></a>Konflikty współbieżności

Konflikt współbieżności występuje, gdy jeden użytkownik wyświetla dane jednostki w celu jej edycji, a następnie inny użytkownik aktualizuje dane tej samej jednostki przed zapisaniem pierwszej zmiany użytkownika w bazie danych. Jeśli nie włączysz wykrywania takich konfliktów, ten, kto aktualizuje bazę danych ostatnio, zastępuje zmiany innego użytkownika. W wielu aplikacjach to ryzyko jest dopuszczalne: jeśli istnieje kilka użytkowników lub kilka aktualizacji lub jeśli nie jest naprawdę krytyczne, jeśli niektóre zmiany są zastępowane, koszt programowania współbieżności może przeważyć korzyści. W takim przypadku nie trzeba skonfigurować aplikację do obsługi konfliktów współbieżności.

### <a name="pessimistic-concurrency-locking"></a>Współbieżność pesymistyczna (blokowanie)

Jeśli aplikacja musi zapobiec przypadkowej utracie danych w scenariuszach współbieżności, jednym ze sposobów, aby to zrobić, jest użycie blokad bazy danych. Jest to tak zwana współbieżność pesymistyczna. Na przykład przed odczytaniem wiersza z bazy danych, należy zażądać blokady tylko do odczytu lub dostępu do aktualizacji. Jeśli zablokujesz wiersz dostępu do aktualizacji, żaden inny użytkownik nie może zablokować wiersza tylko do odczytu lub dostępu do aktualizacji, ponieważ otrzymają kopię danych, które są w trakcie zmiany. Jeśli zablokujesz wiersz dla dostępu tylko do odczytu, inne osoby mogą również zablokować go w celu uzyskania dostępu tylko do odczytu, ale nie do aktualizacji.

Zarządzanie blokadami ma wady. Program może być złożony. Wymaga znacznych zasobów zarządzania bazami danych i może powodować problemy z wydajnością wraz ze wzrostem liczby użytkowników aplikacji. Z tych powodów nie wszystkie systemy zarządzania bazami danych obsługują pesymistyczną współbieżność. Entity Framework Core zapewnia nie wbudowaną obsługę dla niego i ten samouczek nie pokazuje, jak go zaimplementować.

### <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Alternatywą dla pesymistycznej współbieżności jest optymistyczna współbieżność. Optymistyczna współbieżność oznacza, że zezwala na konflikty współbieżności, a następnie odpowiednio reaguje, jeśli tak się stanie. Na przykład Jane odwiedza stronę Edycja działu i zmienia kwotę budżetu dla działu angielskiego z 350 000,00 USD na 0,00 USD.

![Zmiana budżetu na 0](concurrency/_static/change-budget.png)

Zanim Jane kliknie **przycisk Zapisz,** Jan odwiedza tę samą stronę i zmienia pole Data rozpoczęcia z 1/2007 na 9/1/2013.

![Zmiana daty rozpoczęcia na 2013 r.](concurrency/_static/change-date.png)

Jane klika **najpierw przycisk Zapisz** i widzi jej zmianę, gdy przeglądarka powróci do strony Indeks.

![Budżet zmieniony na zero](concurrency/_static/budget-zero.png)

Następnie Jan klika **zapisz** na stronie Edycji, która nadal pokazuje budżet 350 000,00 USD. Co dzieje się dalej zależy od sposobu obsługi konfliktów współbieżności.

Niektóre z opcji są następujące:

* Można śledzić, która właściwość użytkownik zmodyfikował i zaktualizować tylko odpowiednie kolumny w bazie danych.

     W przykładowym scenariuszu żadne dane nie zostaną utracone, ponieważ różne właściwości zostały zaktualizowane przez dwóch użytkowników. Następnym razem, gdy ktoś przegląda angielski dział, zobaczy zmiany zarówno Jane, jak i Johna - datę rozpoczęcia 1/013 i budżet zero dolarów. Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogą spowodować utratę danych, ale nie można uniknąć utraty danych, jeśli konkurencyjne zmiany są wprowadzane do tej samej właściwości jednostki. Czy Entity Framework działa w ten sposób zależy od sposobu zaimplementowania kodu aktualizacji. Często nie jest praktyczne w aplikacji sieci web, ponieważ może wymagać, aby zachować duże ilości stanu w celu śledzenia wszystkich wartości oryginalnej właściwości dla jednostki, jak również nowe wartości. Utrzymywanie dużych ilości stanu może mieć wpływ na wydajność aplikacji, ponieważ wymaga zasobów serwera lub musi być uwzględniony w samej stronie sieci web (na przykład w ukrytych polach) lub w pliku cookie.

* Możesz pozwolić, aby zmiana Johna nadpisyła zmianę Jane.

     Następnym razem, gdy ktoś przegląda dział angielski, zobaczy 9/1/2013 i przywróconą wartość $350,000.00. Jest to tak zwany *scenariusz Wins klienta* lub Ostatni *w* wins. (Wszystkie wartości od klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jak wspomniano we wstępie do tej sekcji, jeśli nie zrobisz żadnego kodowania do obsługi współbieżności, nastąpi to automatycznie.

* Można zapobiec zmiany Jana z aktualizowaniem w bazie danych.

     Zazwyczaj wyświetlany jest komunikat o błędzie, pokazywać mu bieżący stan danych i umożliwiać ponowne zastosowanie zmian, jeśli nadal chce je wprowadzić. Jest to tak zwany scenariusz *Wygrane sklepu.* (Wartości magazynu danych mają pierwszeństwo przed wartościami przesłane przez klienta). W tym samouczku zaimplementujesz scenariusz Wygrane sklepu. Ta metoda gwarantuje, że żadne zmiany nie są zastępowane bez użytkownika jest powiadamiany o tym, co się dzieje.

### <a name="detecting-concurrency-conflicts"></a>Wykrywanie konfliktów współbieżności

Można rozwiązać konflikty, obsługując `DbConcurrencyException` wyjątki, które zgłasza Entity Framework. Aby wiedzieć, kiedy zgłosić te wyjątki, entity Framework musi być w stanie wykryć konflikty. W związku z tym należy odpowiednio skonfigurować bazę danych i model danych. Oto niektóre opcje włączania wykrywania konfliktów:

* W tabeli bazy danych dołącz kolumnę śledzenia, która może służyć do określenia, kiedy wiersz został zmieniony. Następnie można skonfigurować Entity Framework, aby uwzględnić tę kolumnę w Where klauzuli SQL Update lub Delete poleceń.

     Typ danych kolumny śledzenia jest `rowversion`zazwyczaj . Wartość `rowversion` jest numerem sekwencyjnym, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany. W poleceniu Aktualizuj lub Usuń klauzula Where zawiera oryginalną wartość kolumny śledzenia (oryginalna wersja wiersza). Jeśli zaktualizowany wiersz został zmieniony przez innego użytkownika, wartość w `rowversion` kolumnie jest inna niż oryginalna wartość, więc update lub Delete instrukcji nie można znaleźć wiersz do aktualizacji z powodu Where klauzuli. Gdy entity Framework stwierdzi, że żadne wiersze nie zostały zaktualizowane przez update lub Delete polecenia (to znaczy, gdy liczba wierszy dotyczy wynosi zero), interpretuje to jako konflikt współbieżności.

* Skonfiguruj entity framework, aby uwzględnić oryginalne wartości każdej kolumny w tabeli w Where klauzuli Update i Delete poleceń.

     Podobnie jak w pierwszej opcji, jeśli cokolwiek w wierszu uległa zmianie od czasu pierwszego odczytu wiersza, klauzula Where nie zwróci wiersza do aktualizacji, który entity framework interpretuje jako konflikt współbieżności. W przypadku tabel bazy danych, które mają wiele kolumn, takie podejście może spowodować bardzo duże klauzule Where i może wymagać utrzymania dużych ilości stanu. Jak wspomniano wcześniej, utrzymanie dużych ilości stanu może mieć wpływ na wydajność aplikacji. W związku z tym takie podejście nie jest ogólnie zalecane i nie jest to metoda używana w tym samouczku.

     Jeśli chcesz zaimplementować to podejście do współbieżności, należy oznaczyć wszystkie właściwości klucza niepodstawowego w jednostce, dla której chcesz śledzić współbieżność, dodając do nich `ConcurrencyCheck` atrybut. Ta zmiana umożliwia Entity Framework do uwzględnienia wszystkich kolumn w SQL Where klauzuli Update i Delete instrukcji.

W pozostałej części tego samouczka `rowversion` dodasz właściwość śledzenia do jednostki Dział, utworzysz kontroler i widoki oraz przetestujesz, aby sprawdzić, czy wszystko działa poprawnie.

## <a name="add-a-tracking-property"></a>Dodawanie właściwości śledzenia

W *models/department.cs*dodaj właściwość śledzenia o nazwie RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Atrybut `Timestamp` określa, że ta kolumna zostanie uwzględniona w where klauzuli Update i Delete polecenia wysyłane do bazy danych. Atrybut jest wywoływany, `Timestamp` ponieważ poprzednie wersje `timestamp` programu SQL Server `rowversion` używały typu danych SQL, zanim sql zastąpił go. Typ .NET `rowversion` jest tablicą bajtów.

Jeśli wolisz używać płynnego interfejsu API, `IsConcurrencyToken` możesz użyć metody (w *Data/SchoolContext.cs),* aby określić właściwość śledzenia, jak pokazano w poniższym przykładzie:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Dodając właściwość zmieniono model bazy danych, więc należy wykonać inną migrację.

Zapisz zmiany i skompiluj projekt, a następnie wprowadź następujące polecenia w oknie polecenia:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Tworzenie kontrolera i widoków działów

Szkielet kontrolera działów i widoki, jak to miało miejsce wcześniej dla studentów, kursów i instruktorów.

![Dział rusztowania](concurrency/_static/add-departments-controller.png)

W pliku *DepartmentsController.cs* zmień wszystkie cztery wystąpienia "FirstMidName" na "FullName", aby listy rozwijane administratora działu zawierały pełną nazwę instruktora, a nie tylko nazwisko.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Aktualizuj widok indeksu

Aparat szkieletu utworzył kolumnę RowVersion w widoku Indeks, ale to pole nie powinno być wyświetlane.

Zastąp kod w *views/departments/index.cshtml* następującym kodem.

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Spowoduje to zmianę nagłówka na "Działy", powoduje usunięcie kolumny RowVersion i pokazuje pełną nazwę zamiast imienia administratora.

## <a name="update-edit-methods"></a>Aktualizuj metody edycji

Zarówno w httpget `Edit` metody `Details` i `AsNoTracking`metody, dodaj . W HttpGet `Edit` metody, dodaj wczesne ładowanie dla administratora.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Zastąp istniejący `Edit` kod metody HttpPost następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Kod rozpoczyna się od próby odczytania działu, który ma zostać zaktualizowany. Jeśli `FirstOrDefaultAsync` metoda zwraca wartość null, dział został usunięty przez innego użytkownika. W takim przypadku kod używa zaksięgowanych wartości formularza do utworzenia encji działu, dzięki czemu strona Edytuj może zostać ponownie wyświetlona za pomocą komunikatu o błędzie. Alternatywnie nie trzeba będzie ponownie utworzyć jednostki działu, jeśli wyświetlany jest tylko komunikat o błędzie bez ponownego wyświetlania pól działu.

Widok przechowuje `RowVersion` oryginalną wartość w polu ukrytym, a `rowVersion` ta metoda odbiera tę wartość w parametrze. Przed wywołaniem `SaveChanges`, należy umieścić `RowVersion` tę oryginalną wartość właściwości w `OriginalValues` kolekcji dla jednostki.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Następnie, gdy entity framework tworzy polecenie SQL UPDATE, to polecenie będzie zawierać klauzulę WHERE, która wyszukuje wiersz, który ma oryginalną `RowVersion` wartość. Jeśli polecenie UPDATE nie ma wpływu na żadne wiersze `RowVersion` (żadne wiersze nie `DbUpdateConcurrencyException` mają oryginalnej wartości), entity framework zgłasza wyjątek.

Kod w bloku catch dla tego wyjątku pobiera jednostkę departamentu, którego dotyczy problem, która ma zaktualizowane wartości z `Entries` właściwości na obiekcie wyjątku.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

Kolekcja `Entries` będzie miała `EntityEntry` tylko jeden obiekt.  Można użyć tego obiektu, aby uzyskać nowe wartości wprowadzone przez użytkownika i bieżące wartości bazy danych.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych inne niż to, co użytkownik wprowadził na stronie Edycja (tylko jedno pole jest wyświetlane tutaj dla zwięzłości).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Na koniec kod ustawia `RowVersion` wartość `departmentToUpdate` do nowej wartości pobranej z bazy danych. Ta `RowVersion` nowa wartość będzie przechowywana w polu ukrytym po ponownym wyświetleniu strony Edytuj, a następnym kliknięciem przez użytkownika **przycisku Zapisz**zostaną przechwycone tylko błędy współbieżności, które wystąpią od czasu ponownego wyświetlenia strony Edytuj.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

Instrukcja `ModelState.Remove` jest wymagana, `ModelState` ponieważ `RowVersion` ma starą wartość. W widoku `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy oba są obecne.

## <a name="update-edit-view"></a>Aktualizuj widok edycji

W *widokach/działach/edit.cshtml*wykonuj następujące zmiany:

* Dodaj ukryte pole, `RowVersion` aby zapisać wartość właściwości, natychmiast `DepartmentID` po ukrytym polu właściwości.

* Dodaj opcję "Wybierz administratora" do listy rozwijanej.

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Testowanie konfliktów współbieżności

Uruchom aplikację i przejdź do strony Indeks działów. Kliknij prawym przyciskiem myszy **hiperłącze Edytuj** dla działu angielskiego i wybierz pozycję Otwórz na **nowej karcie**, a następnie kliknij polecenie Edytuj **hiperłącze** dla działu angielskiego. Dwie karty przeglądarki wyświetlają teraz te same informacje.

Zmień pole na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.

![Edycja działu strona 1 po zmianie](concurrency/_static/edit-after-change-1.png)

Przeglądarka pokazuje stronę Indeks ze zmienioną wartością.

Zmienianie pola na drugiej karcie przeglądarki.

![Edycja działu strona 2 po zmianie](concurrency/_static/edit-after-change-2.png)

Kliknij przycisk **Zapisz**. Zostanie wyświetlony komunikat o błędzie:

![Komunikat o błędzie strony edycji działu](concurrency/_static/edit-error.png)

Kliknij **pozycję Zapisz** ponownie. Wartość wprowadzona na drugiej karcie przeglądarki zostanie zapisana. Po wyświetleniu strony Indeksu są wyświetlane zapisane wartości.

## <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

Dla strony Usuń Entity Framework wykrywa konflikty współbieżności spowodowane przez kogoś innego edycji działu w podobny sposób. Gdy HttpGet `Delete` metoda wyświetla widok potwierdzenia, widok `RowVersion` zawiera oryginalną wartość w polu ukrytym. Ta wartość jest następnie dostępna `Delete` dla metody HttpPost, która jest wywoływana, gdy użytkownik potwierdzi usunięcie. Gdy entity framework tworzy polecenie SQL DELETE, zawiera klauzulę WHERE z oryginalną `RowVersion` wartością. Jeśli polecenie powoduje zero wierszy dotyczy (co oznacza, że wiersz został zmieniony po delete stronie potwierdzenia został wyświetlony), wyjątek współbieżności jest generowany, a HttpGet `Delete` metoda jest wywoływana z flagą błędu ustawioną na true w celu ponownego wyświetlenia strony potwierdzenia z komunikatem o błędzie. Jest również możliwe, że zero wierszy zostały naruszone, ponieważ wiersz został usunięty przez innego użytkownika, więc w tym przypadku nie jest wyświetlany żaden komunikat o błędzie.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Aktualizowanie metod usuwania w kontrolerze działów

W *DepartmentsController.cs*, zastąp `Delete` HttpGet metody z następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Metoda akceptuje opcjonalny parametr, który wskazuje, czy strona jest ponownie świetlana po błędzie współbieżności. Jeśli ta flaga jest true i dział określony już nie istnieje, został usunięty przez innego użytkownika. W takim przypadku kod przekierowuje do indeksu strony.  Jeśli ta flaga jest true i Dział istnieje, został zmieniony przez innego użytkownika. W takim przypadku kod wysyła komunikat o `ViewData`błędzie do widoku za pomocą .

Zastąp kod `Delete` w metodzie HttpPost (o nazwie) `DeleteConfirmed`następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

W kodzie szkieletowym, który właśnie został zastąpiony, ta metoda zaakceptowała tylko identyfikator rekordu:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Ten parametr został zmieniony na wystąpienie jednostki dział utworzone przez spinacza modelu. Daje to EF dostęp do wartości właściwości RowVersion oprócz klucza rekordu.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Zmieniono również nazwę metody `DeleteConfirmed` akcji `Delete`na . Szkieletowy kod użył nazwy, `DeleteConfirmed` aby nadać metodzie HttpPost unikatowy podpis. (CLR wymaga przeciążonych metod, aby mieć różne parametry metody.) Teraz, gdy podpisy są unikatowe, można trzymać się konwencji MVC i używać tej samej nazwy dla HttpPost i HttpGet delete metody.

Jeśli dział jest już `AnyAsync` usunięty, metoda zwraca false i aplikacja po prostu wraca do Index metody.

Jeśli zostanie przechwycony błąd współbieżności, kod ponownie wyświetla stronę potwierdzenia usuń i udostępnia flagę, która wskazuje, że powinien wyświetlać komunikat o błędzie współbieżności.

### <a name="update-the-delete-view"></a>Aktualizowanie widoku Usuń

W *widokach/departments/delete.cshtml*zastąp kod szkieletu następującym kodem, który dodaje pole komunikatu o błędzie i ukryte pola dla właściwości DepartmentID i RowVersion. Zmiany są wyróżnione.

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Powoduje to następujące zmiany:

* Dodaje komunikat o `h2` błędzie `h3` między nagłówkami a nagłówkami.

* Zastępuje program FirstMidName na fullname w polu **Administrator.**

* Usuwa pole WierszVersion.

* Dodaje ukryte pole `RowVersion` dla właściwości.

Uruchom aplikację i przejdź do strony Indeks działów. Kliknij prawym przyciskiem myszy **usuń** hiperłącze dla działu angielskiego i wybierz pozycję **Otwórz na nowej karcie,** a następnie na pierwszej karcie kliknij polecenie Edytuj hiperłącze dla działu angielskiego. **Edit**

W pierwszym oknie zmień jedną z wartości i kliknij przycisk **Zapisz**:

![Strona Edycji działu po zmianie przed usunięciem](concurrency/_static/edit-after-change-for-delete.png)

Na drugiej karcie kliknij pozycję **Usuń**. Zostanie wyświetlony komunikat o błędzie współbieżności, a wartości działu są odświeżane z tym, co jest obecnie w bazie danych.

![Strona potwierdzenia usunięcia departamentu z błędem współbieżności](concurrency/_static/delete-error.png)

Jeśli klikniesz ponownie **przycisk Usuń,** zostaniesz przekierowany do strony Indeks, która pokazuje, że dział został usunięty.

## <a name="update-details-and-create-views"></a>Aktualizowanie szczegółów i tworzenie widoków

Opcjonalnie można oczyścić szkieletu kodu w Szczegóły i Tworzenie widoków.

Zastąp kod w *widokach/departamentach/details.cshtml,* aby usunąć kolumnę RowVersion i wyświetlić pełną nazwę administratora.

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Zastąp kod w *widokach/działach/create.cshtml,* aby dodać opcję Wybierz do listy rozwijanej.

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Zasoby dodatkowe

 Aby uzyskać więcej informacji na temat obsługi współbieżności w EF Core, zobacz [Konflikty współbieżności](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dowiedz się więcej o konfliktach współbieżności
> * Dodano właściwość śledzenia
> * Utworzony kontroler i widoki działów
> * Zaktualizowany widok indeksu
> * Zaktualizowane metody edycji
> * Zaktualizowany widok edycji
> * Przetestowane konflikty współbieżności
> * Zaktualizowano stronę Usuwanie
> * Zaktualizowano szczegóły i tworzenie widoków

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaimplementować dziedziczenie tabeli na hierarchię dla jednostek Instruktor i Student.

> [!div class="nextstepaction"]
> [Dalej: Implementowanie dziedziczenia tabeli na hierarchię](inheritance.md)
