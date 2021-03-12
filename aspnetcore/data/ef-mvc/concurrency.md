---
title: 'Samouczek: obsługa współbieżności ASP.NET MVC z EF Core'
description: W tym samouczku pokazano, jak obsłużyć konflikty, gdy wielu użytkowników jednocześnie zaktualizuje tę samą jednostkę.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: cc93069899953d04d1df4e79a282c349a163b06e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586777"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Samouczek: obsługa współbieżności ASP.NET MVC z EF Core

W poprzednich samouczkach przedstawiono sposób aktualizowania danych. W tym samouczku pokazano, jak obsłużyć konflikty, gdy wielu użytkowników jednocześnie zaktualizuje tę samą jednostkę.

Utworzysz strony sieci Web, które współpracują z jednostką działu i obsługują błędy współbieżności. Na poniższych ilustracjach przedstawiono strony edycji i usuwania, w tym niektóre komunikaty, które są wyświetlane w przypadku wystąpienia konfliktu współbieżności.

![Strona edycji działu](concurrency/_static/edit-error.png)

![Strona usuwania działu](concurrency/_static/delete-error.png)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Informacje o konfliktach współbieżności
> * Dodaj właściwość śledzenia
> * Tworzenie kontrolera i widoków działów
> * Aktualizuj widok indeksu
> * Aktualizowanie metod edycji
> * Aktualizuj widok edycji
> * Testuj konflikty współbieżności
> * Aktualizowanie strony usuwania
> * Aktualizowanie szczegółów i Tworzenie widoków

## <a name="prerequisites"></a>Wymagania wstępne

* [Aktualizowanie powiązanych danych](update-related-data.md)

## <a name="concurrency-conflicts"></a>Konflikty współbieżności

Konflikt współbieżności występuje, gdy jeden użytkownik wyświetla dane jednostki w celu ich edycji, a następnie inny użytkownik aktualizuje dane tej samej jednostki przed zapisaniem w bazie danych pierwszego użytkownika. Jeśli nie włączysz wykrywania takich konfliktów, osoba, która aktualizuje bazę danych, ostatnio zastępuje zmiany wprowadzone przez innych użytkowników. W wielu aplikacjach to ryzyko jest akceptowalne: w przypadku kilku użytkowników lub kilku aktualizacji lub jeśli nie jest to naprawdę krytyczne, jeśli niektóre zmiany zostaną nadpisywane, koszt programowania współbieżności może wznieść korzyści. W takim przypadku nie trzeba konfigurować aplikacji do obsługi konfliktów współbieżności.

### <a name="pessimistic-concurrency-locking"></a>Współbieżność pesymistyczna (blokowanie)

Jeśli aplikacja musi zapobiegać przypadkowej utracie danych w scenariuszach współbieżności, jeden ze sposobów jest używany do blokowania baz danych. Jest to nazywane pesymistyczną współbieżnością. Na przykład przed odczytaniem wiersza z bazy danych należy zażądać blokady dla dostępu tylko do odczytu lub do aktualizacji. Jeśli zablokujesz wiersz na potrzeby dostępu do aktualizacji, żaden inny użytkownik nie będzie mógł zablokować wiersza dla dostępu tylko do odczytu lub aktualizacji, ponieważ spowodowałoby to skopiowanie danych w procesie. Jeśli zablokujesz wiersz dla dostępu tylko do odczytu, inne osoby mogą także zablokować dostęp tylko do odczytu, ale nie dla aktualizacji.

Zarządzanie blokadami ma wady. Może być skomplikowany dla programu. Wymaga to znaczących zasobów zarządzania bazami danych. może to spowodować problemy z wydajnością w miarę zwiększania się liczby użytkowników aplikacji. Z tego względu nie wszystkie systemy zarządzania bazami danych obsługują pesymistyczne współbieżności. Entity Framework Core nie zapewnia wbudowanej pomocy technicznej i ten samouczek nie pokazuje, jak wdrożyć go.

### <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Alternatywą dla pesymistycznej współbieżności jest Optymistyczna współbieżność. Współbieżność optymistyczna pozwala na wykonywanie konfliktów współbieżności, a następnie podejmowanie odpowiednich działań. Na przykład Joanna odwiedzi stronę Edycja działu i zmieni kwotę budżetu dla działu angielskiego z $350 000,00 na $0,00.

![Zmiana wartości budżetu na 0](concurrency/_static/change-budget.png)

Przed Janem kliknie przycisk **Zapisz**, Jan odwiedzi tę samą stronę i zmieni pole Data rozpoczęcia z 9/1/2007 na 9/1/2013.

![Zmiana daty rozpoczęcia na 2013](concurrency/_static/change-date.png)

Jan klika pozycję **Zapisz** jako pierwszy i widzi zmiany, gdy przeglądarka powraca do strony indeks.

![Budżet zmieniony na zero](concurrency/_static/budget-zero.png)

Następnie Jan klika pozycję **Zapisz** na stronie edytowania, która nadal zawiera budżet $350 000,00. Co się stanie dalej, zależy od sposobu obsługi konfliktów współbieżności.

Dostępne są następujące opcje:

* Można śledzić, która właściwość została zmodyfikowana przez użytkownika i zaktualizować tylko odpowiednie kolumny w bazie danych.

     W przykładowym scenariuszu żadne dane nie zostaną utracone, ponieważ różne właściwości zostały zaktualizowane przez dwóch użytkowników. Następnym razem, gdy ktoś przegląda ten dział w języku angielskim, zobaczy zmiany w kategorii Janina i Jan — datę początkową 9/1/2013 i budżet zerowych dolarów. Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogłyby spowodować utratę danych, ale nie może uniknąć utraty danych, jeśli wprowadzono konkurencyjne zmiany w tej samej właściwości jednostki. Czy Entity Framework działa w ten sposób, zależy od sposobu implementacji kodu aktualizacji. Często nie jest to praktyczne w aplikacji sieci Web, ponieważ może wymagać utrzymania dużej ilości danych w celu śledzenia wszystkich oryginalnych wartości właściwości dla jednostki, a także nowych wartości. Utrzymywanie dużej ilości stanu może wpłynąć na wydajność aplikacji, ponieważ wymaga ona zasobów serwera lub musi być uwzględniona na stronie sieci Web (na przykład w ukrytych polach) lub w cookie .

* Możesz pozwolić, aby zmiana została zastąpiona przez Jan.

     Następnym razem, gdy ktoś przegląda dział w języku angielskim, zobaczy 9/1/2013 i przywrócona wartość $350 000,00. Jest to tzw. *klient WINS* lub *ostatni w scenariuszu usługi WINS* . (Wszystkie wartości z klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jak zostało to opisane we wprowadzeniu do tej sekcji, jeśli nie wykonasz kodowania na potrzeby obsługi współbieżności, zostanie to wykonane automatycznie.

* Można zapobiec aktualizacji firmy Jan ze zmian w bazie danych.

     Zwykle zostanie wyświetlony komunikat o błędzie, wyświetlenie go w bieżącym stanie danych i umożliwienie mu ponownego zastosowania zmian w przypadku, gdy nadal chce je wprowadzić. Jest to tzw. scenariusz *magazynu usługi WINS* . (Wartości ze sklepu danych mają pierwszeństwo przed wartościami przesyłanymi przez klienta). W tym samouczku zostanie wdrożony scenariusz sklepu WINS. Ta metoda zapewnia, że żadne zmiany nie są zastępowane bez alertu użytkownika o tym, co się dzieje.

### <a name="detecting-concurrency-conflicts"></a>Wykrywanie konfliktów współbieżności

Konflikty można rozwiązać przez obsługę `DbConcurrencyException` wyjątków zgłaszanych przez Entity Framework. Aby dowiedzieć się, kiedy należy zgłosić te wyjątki, Entity Framework musi mieć możliwość wykrywania konfliktów. W związku z tym należy odpowiednio skonfigurować bazę danych i model danych. Dostępne są następujące opcje włączania wykrywania konfliktów:

* W tabeli bazy danych Dołącz kolumnę śledzenia, której można użyć do określenia, kiedy wiersz został zmieniony. Następnie można skonfigurować Entity Framework, aby uwzględnić tę kolumnę w klauzuli WHERE polecenia SQL Update lub DELETE.

     Typ danych kolumny śledzenia jest zwykle `rowversion` . `rowversion`Wartość jest kolejnym numerem, który jest zwiększany za każdym razem, gdy wiersz zostanie zaktualizowany. W przypadku polecenia Update lub DELETE klauzula WHERE zawiera oryginalną wartość kolumny śledzenia (oryginalną wersję wiersza). Jeśli aktualizowany wiersz został zmieniony przez innego użytkownika, wartość w `rowversion` kolumnie jest inna niż oryginalna wartość, więc instrukcja UPDATE lub DELETE nie może znaleźć wiersza do zaktualizowania z powodu klauzuli WHERE. Gdy Entity Framework stwierdzi, że żadne wiersze nie zostały zaktualizowane przez polecenie Update lub Delete (oznacza to, że gdy liczba odnośnych wierszy wynosi zero), interpretuje to jako konflikt współbieżności.

* Skonfiguruj Entity Framework, aby uwzględnić oryginalne wartości każdej kolumny w tabeli w klauzuli WHERE poleceń Update i DELETE.

     Jak w pierwszej opcji, jeśli coś w wierszu uległo zmianie od momentu pierwszego odczytu wiersza, klauzula WHERE nie zwraca wiersza do zaktualizowania, który Entity Framework interpretuje jako konflikt współbieżności. W przypadku tabel bazy danych z wieloma kolumnami takie podejście może skutkować bardzo dużymi klauzulami WHERE i może wymagać utrzymania dużej ilości danych. Jak wspomniano wcześniej, utrzymanie dużej ilości stanu może wpłynąć na wydajność aplikacji. W związku z tym takie podejście zwykle nie jest zalecane i nie jest to metoda używana w tym samouczku.

     Jeśli chcesz zaimplementować to podejście do współbieżności, musisz oznaczyć wszystkie właściwości klucza niepodstawowego w jednostce, dla której chcesz śledzić współbieżność, dodając `ConcurrencyCheck` do nich atrybut. Ta zmiana umożliwia Entity Framework uwzględnienie wszystkich kolumn w klauzuli SQL WHERE instrukcji UPDATE i DELETE.

W pozostałej części tego samouczka dodasz `rowversion` Właściwość śledzenia do jednostki działu, utworzysz kontroler i widoki i testujesz, aby sprawdzić, czy wszystko działa prawidłowo.

## <a name="add-a-tracking-property"></a>Dodaj właściwość śledzenia

W obszarze *modele/dział. cs* Dodaj właściwość śledzenia o nazwie rowversion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Ten `Timestamp` atrybut określa, że ta kolumna zostanie uwzględniona w klauzuli WHERE poleceń Update i DELETE wysyłanych do bazy danych. Ten atrybut jest wywoływany, `Timestamp` ponieważ poprzednie wersje SQL Server używały `timestamp` typu danych SQL przed zastąpieniem go przez program SQL Server `rowversion` . Typ .NET dla `rowversion` jest tablicą bajtów.

Jeśli wolisz używać interfejsu API Fluent, możesz użyć `IsConcurrencyToken` metody (w *danych/SchoolContext. cs*), aby określić właściwość śledzenia, jak pokazano w następującym przykładzie:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Przez dodanie właściwości, która zmieniła model bazy danych, należy wykonać kolejną migrację.

Zapisz zmiany i skompiluj projekt, a następnie wprowadź następujące polecenia w oknie polecenia:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Tworzenie kontrolera i widoków działów

Tworzy szkielet na kontrolerze i widokach działów jak wcześniej dla studentów, kursów i instruktorów.

![Dział szkieletu](concurrency/_static/add-departments-controller.png)

W pliku *DepartmentsController.cs* Zmień wszystkie cztery wystąpienia elementu "FirstMidName" na "FullName", dzięki czemu listy rozwijane administrator działu będą zawierać pełną nazwę instruktora, a nie tylko nazwisko.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Aktualizuj widok indeksu

Aparat tworzenia szkieletów utworzył kolumnę RowVersion w widoku indeks, ale to pole nie powinno być wyświetlane.

Zastąp kod w *widokach/działach/index. cshtml* następującym kodem.

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Spowoduje to zmianę nagłówka na "działy", usunięcie kolumny RowVersion i wyświetlenie pełnej nazwy zamiast imienia administratora.

## <a name="update-edit-methods"></a>Aktualizowanie metod edycji

W `Edit` metodzie narzędzia HttpGet i `Details` metodzie Dodaj `AsNoTracking` . W `Edit` metodzie narzędzia HttpGet Dodaj eager ładowania dla administratora.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Zastąp istniejący kod `Edit` metody HTTPPOST następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Kod rozpoczyna się od próby odczytu działu do zaktualizowania. Jeśli `FirstOrDefaultAsync` Metoda zwraca wartość null, dział został usunięty przez innego użytkownika. W takim przypadku kod używa opublikowanych wartości formularza do utworzenia jednostki działu, aby można było ponownie wyświetlić stronę edytowania z komunikatem o błędzie. Alternatywnie nie trzeba ponownie tworzyć jednostki działu, jeśli zostanie wyświetlony komunikat o błędzie bez ponownego wyświetlania pól działu.

Widok przechowuje oryginalną `RowVersion` wartość w ukrytym polu, a ta metoda otrzymuje tę wartość w `rowVersion` parametrze. Przed wywołaniem należy `SaveChanges` umieścić tę oryginalną `RowVersion` wartość właściwości w `OriginalValues` kolekcji dla jednostki.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Następnie, gdy Entity Framework tworzy polecenie SQL UPDATE, to polecenie będzie zawierać klauzulę WHERE, która szuka wiersza, który ma oryginalną `RowVersion` wartość. Jeśli nie ma żadnych wierszy, których dotyczy polecenie aktualizacji (żadne wiersze nie mają oryginalnej `RowVersion` wartości), Entity Framework zgłasza `DbUpdateConcurrencyException` wyjątek.

Kod w bloku catch dla tego wyjątku pobiera jednostkę działu, której dotyczy usterka, która ma zaktualizowane wartości z `Entries` właściwości obiektu wyjątku.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

`Entries`Kolekcja będzie zawierać tylko jeden `EntityEntry` obiekt.  Można użyć tego obiektu, aby pobrać nowe wartości wprowadzone przez użytkownika i bieżące wartości bazy danych.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych inne niż wprowadzone przez użytkownika na stronie edytowania (w tym miejscu jest wyświetlane tylko jedno pole dla zwięzłości).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Na koniec kod ustawia `RowVersion` wartość `departmentToUpdate` do nowej wartości pobranej z bazy danych. Ta nowa `RowVersion` wartość zostanie zapisana w ukrytym polu po ponownym wyświetleniu strony edycji, a przy następnym kliknięciu przycisku **Zapisz** zostaną przechwycone tylko błędy współbieżności, które zachodzą od momentu wyświetlenia ekranu edycji.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

`ModelState.Remove`Instrukcja jest wymagana, ponieważ `ModelState` ma starą `RowVersion` wartość. W widoku `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy są obecne oba typy.

## <a name="update-edit-view"></a>Aktualizuj widok edycji

W obszarze *widoki/działy/Edit. cshtml* wprowadź następujące zmiany:

* Dodaj ukryte pole, aby zapisać `RowVersion` wartość właściwości, bezpośrednio po ukrytym polu `DepartmentID` właściwości.

* Dodaj opcję "Wybierz administratora" do listy rozwijanej.

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Testuj konflikty współbieżności

Uruchom aplikację i przejdź do strony indeks działów. Kliknij prawym przyciskiem myszy hiperłącze **Edytuj** dla działu angielskiego i wybierz polecenie **Otwórz na nowej karcie**, a następnie kliknij hiperłącze **Edytuj** dla działu angielskiego. Dwie karty przeglądarki zawierają teraz te same informacje.

Zmień pole na pierwszej karcie przeglądarki, a następnie kliknij przycisk **Zapisz**.

![Edycja działu Strona 1 po zmianie](concurrency/_static/edit-after-change-1.png)

W przeglądarce zostanie wyświetlona strona indeks o zmienionej wartości.

Zmień pole na drugiej karcie przeglądarki.

![Edycja działu Strona 2 po zmianie](concurrency/_static/edit-after-change-2.png)

Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat o błędzie:

![Komunikat o błędzie strony edytowania działu](concurrency/_static/edit-error.png)

Kliknij przycisk **Zapisz** ponownie. Wartość wprowadzona na drugiej karcie przeglądarki jest zapisywana. Zapisane wartości są wyświetlane, gdy zostanie wyświetlona strona indeks.

## <a name="update-the-delete-page"></a>Aktualizowanie strony usuwania

Na stronie Usuwanie Entity Framework wykrywa konflikty współbieżności spowodowane przez inną osobę edytującą dział w podobny sposób. Gdy metoda narzędzia HttpGet `Delete` wyświetla widok potwierdzenia, widok zawiera oryginalną `RowVersion` wartość w ukrytym polu. Ta wartość jest następnie dostępna dla metody HttpPost `Delete` , która jest wywoływana, gdy użytkownik potwierdzi usunięcie. Gdy Entity Framework tworzy polecenie SQL DELETE, zawiera klauzulę WHERE z oryginalną `RowVersion` wartością. Jeśli w poleceniu wyniki są równe zero (oznacza to, że wiersz został zmieniony po wyświetleniu strony potwierdzenia usunięcia), zgłaszany jest wyjątek współbieżności, a `Delete` Metoda narzędzia HttpGet jest wywoływana z flagą błędu ustawioną na wartość true, aby ponownie wyświetlić stronę potwierdzenia z komunikatem o błędzie. Istnieje również możliwość, że zero wierszy miało wpływ, ponieważ wiersz został usunięty przez innego użytkownika, więc w takim przypadku nie jest wyświetlany komunikat o błędzie.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Aktualizowanie metod DELETE w kontrolerze działu

W *DepartmentsController.cs* Zastąp metodę narzędzia HttpGet `Delete` następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Metoda przyjmuje opcjonalny parametr, który wskazuje, czy strona jest ponownie wyświetlana po wystąpieniu błędu współbieżności. Jeśli flaga ma wartość true, a określony dział już nie istnieje, został usunięty przez innego użytkownika. W takim przypadku kod przekieruje się do strony indeksu.  Jeśli ta flaga ma wartość true, a dział istnieje, został zmieniony przez innego użytkownika. W takim przypadku kod wysyła komunikat o błędzie do widoku przy użyciu `ViewData` .

Zastąp kod w `Delete` metodzie HTTPPOST (o nazwie `DeleteConfirmed` ) następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

W kodzie szkieletowym, który właśnie został zastąpiony, ta metoda akceptuje tylko identyfikator rekordu:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Ten parametr został zmieniony do wystąpienia jednostki działu utworzonego przez spinacz modelu. Zapewnia to EF dostęp do wartości właściwości RowVersion oprócz klucza rekordu.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Zmieniono także nazwę metody akcji z `DeleteConfirmed` na `Delete` . Kod szkieletu użył nazwy, `DeleteConfirmed` Aby nadać metodzie HTTPPOST unikatową sygnaturę. (Środowisko CLR wymaga przeciążonych metod, aby mieć inne parametry metody). Teraz, gdy podpisy są unikatowe, można naklejić do Konwencji MVC i używać tej samej nazwy dla metod Delete HttpPost i narzędzia HttpGet.

Jeśli dział został już usunięty, `AnyAsync` Metoda zwraca wartość false, a aplikacja po prostu wraca do metody index.

W przypadku przechwyconego błędu współbieżności kod ponownie wyświetla stronę potwierdzenia usuwania i zawiera flagę wskazującą, że powinien zostać wyświetlony komunikat o błędzie współbieżności.

### <a name="update-the-delete-view"></a>Aktualizowanie widoku usuwania

W obszarze *widoki/działy/Delete. cshtml* Zamień kod szkieletowy na następujący kod, który dodaje pole komunikatu o błędzie i ukryte pola dla właściwości DepartmentID i rowversion. Zmiany są wyróżnione.

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Powoduje to wprowadzenie następujących zmian:

* Dodaje komunikat o błędzie między `h2` nagłówki i `h3` .

* Zastępuje FirstMidName z FullName w polu **administrator** .

* Usuwa pole RowVersion.

* Dodaje ukryte pole dla `RowVersion` właściwości.

Uruchom aplikację i przejdź do strony indeks działów. Kliknij prawym przyciskiem myszy hiperłącze **Usuń** dla działu angielskiego i wybierz polecenie **Otwórz na nowej karcie**, a następnie na pierwszej karcie kliknij hiperłącze **Edytuj** dla działu angielskiego.

W pierwszym oknie Zmień jedną z wartości, a następnie kliknij przycisk **Zapisz**:

![Strona Edycja działu po zmianie przed usunięciem](concurrency/_static/edit-after-change-for-delete.png)

Na drugiej karcie kliknij pozycję **Usuń**. Zobaczysz komunikat o błędzie współbieżności, a wartości działu są odświeżane z aktualną wartością w bazie danych.

![Strona potwierdzenia usunięcia działu z błędem współbieżności](concurrency/_static/delete-error.png)

Jeśli klikniesz przycisk **Usuń** ponownie, nastąpi przekierowanie do strony indeks, która pokazuje, że dział został usunięty.

## <a name="update-details-and-create-views"></a>Aktualizowanie szczegółów i Tworzenie widoków

Opcjonalnie można oczyścić kod szkieletowy w szczegółach i w widokach.

Zastąp kod w *widokach/działach/details. cshtml* , aby usunąć kolumnę rowversion i wyświetlić pełną nazwę administratora.

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Zastąp kod w *widokach/działach/Create. cshtml* , aby dodać opcję Select do listy rozwijanej.

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Dodatkowe zasoby

 Aby uzyskać więcej informacji o sposobie obsługi współbieżności w EF Core, zobacz [konflikty współbieżności](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Informacje o konfliktach współbieżności
> * Dodano Właściwość śledzenia
> * Utworzono kontroler i widok wydziałów
> * Zaktualizowany widok indeksu
> * Zaktualizowane metody edycji
> * Zaktualizowano widok edycji
> * Przetestowane konflikty współbieżności
> * Zaktualizowano stronę usuwania
> * Zaktualizowane szczegóły i Tworzenie widoków

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaimplementować dziedziczenie na poziomie tabeli dla instruktorów i jednostek ucznia.

> [!div class="nextstepaction"]
> [Dalej: Implementuj dziedziczenie w hierarchii na poziomie tabeli](inheritance.md)
