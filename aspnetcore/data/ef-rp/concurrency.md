---
title: Strony brzytwy z EF Core w ASP.NET Core - Współbieżność - 8 z 8
author: rick-anderson
description: W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje tę samą jednostkę w tym samym czasie.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c4d43f26ba80e7922c3cbd37d9a5f8e1561b11ad
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656913"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Strony brzytwy z EF Core w ASP.NET Core - Współbieżność - 8 z 8

Rick [Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)i Jon P [Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje jednostkę jednocześnie (w tym samym czasie).

## <a name="concurrency-conflicts"></a>Konflikty współbieżności

Konflikt współbieżności występuje, gdy:

* Użytkownik przechodzi do strony edycji encji.
* Inny użytkownik aktualizuje tę samą jednostkę przed zapisaniem pierwszej zmiany w bazie danych.

Jeśli wykrywanie współbieżności nie jest włączone, kto aktualizuje bazę danych ostatnio zastępuje zmiany innego użytkownika. Jeśli to ryzyko jest dopuszczalne, koszt programowania współbieżności może przeważyć korzyści.

### <a name="pessimistic-concurrency-locking"></a>Współbieżność pesymistyczna (blokowanie)

Jednym ze sposobów zapobiegania konfliktom współbieżności jest użycie blokad bazy danych. Jest to tak zwana współbieżność pesymistyczna. Zanim aplikacja odczyta wiersz bazy danych, który zamierza zaktualizować, żąda blokady. Gdy wiersz jest zablokowany dla dostępu do aktualizacji, żaden inny użytkownik nie mogą zablokować wiersz, dopóki nie zostanie zwolniona pierwsza blokada.

Zarządzanie blokadami ma wady. Program może być złożony i może powodować problemy z wydajnością wraz ze wzrostem liczby użytkowników. Entity Framework Core zapewnia nie wbudowaną obsługę dla niego i ten samouczek nie pokazuje, jak go zaimplementować.

### <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Optymistyczna współbieżność umożliwia konflikty współbieżności się zdarzyć, a następnie reaguje odpowiednio, gdy to robią. Na przykład Jane odwiedza stronę edycji departamentu i zmienia budżet działu angielskiego z 350 000,00 USD na 0,00 USD.

![Zmiana budżetu na 0](concurrency/_static/change-budget30.png)

Zanim Jane kliknie **przycisk Zapisz,** Jan odwiedza tę samą stronę i zmienia pole Data rozpoczęcia z 1/2007 na 9/1/2013.

![Zmiana daty rozpoczęcia na 2013 r.](concurrency/_static/change-date30.png)

Jane klika **najpierw przycisk Zapisz** i widzi, że jej zmiana zaczyna obowiązywać, ponieważ przeglądarka wyświetla stronę Indeks z zerem jako kwotę budżetu.

Jan klika **zapisz** na stronie Edycji, która nadal pokazuje budżet 350 000,00 USD. Co dzieje się dalej zależy od sposobu obsługi konfliktów współbieżności:

* Można śledzić, która właściwość użytkownik zmodyfikował i zaktualizować tylko odpowiednie kolumny w bazie danych.

  W scenariuszu żadne dane nie zostaną utracone. Różne właściwości zostały zaktualizowane przez dwóch użytkowników. Następnym razem, gdy ktoś przegląda angielski dział, zobaczą zmiany zarówno Jane, jak i Johna. Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogą spowodować utratę danych. Takie podejście ma pewne wady:
 
  * Nie można uniknąć utraty danych, jeśli konkurencyjne zmiany są wprowadzane do tej samej właściwości.
  * Ogólnie nie jest praktyczne w aplikacji sieci web. Wymaga utrzymania istotnego stanu w celu śledzenia wszystkich pobranych wartości i nowych wartości. Utrzymywanie dużych ilości stanu może mieć wpływ na wydajność aplikacji.
  * Może zwiększyć złożoność aplikacji w porównaniu do wykrywania współbieżności w jednostce.

* Możesz pozwolić, aby zmiana Johna nadpisyła zmianę Jane.

  Następnym razem, gdy ktoś przegląda dział angielski, zobaczy 9/1/2013 i pobraną wartość $350,000.00. Takie podejście jest nazywany *klient wins* lub *Ostatni w* wins scenariusza. (Wszystkie wartości od klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jeśli nie wykonasz żadnego kodowania do obsługi współbieżności, wygrywa klient odbywa się automatycznie.

* Można zapobiec zmiany Jana z aktualizowaniem w bazie danych. Zazwyczaj aplikacja będzie:

  * Wyświetl komunikat o błędzie.
  * Pokaż bieżący stan danych.
  * Zezwalaj użytkownikowi na ponowne zastosowanie zmian.

  Jest to tak zwany scenariusz *Wygrane sklepu.* (Wartości magazynu danych mają pierwszeństwo przed wartościami przesłane przez klienta). Zaimplementowanie scenariusza Wygrane sklepu w tym samouczku. Ta metoda gwarantuje, że żadne zmiany nie są zastępowane bez użytkownika jest alerty.

## <a name="conflict-detection-in-ef-core"></a>Wykrywanie konfliktów w EF Core

EF Core `DbConcurrencyException` zgłasza wyjątki, gdy wykryje konflikty. Model danych musi być skonfigurowany tak, aby włączyć wykrywanie konfliktów. Opcje włączania wykrywania konfliktów są następujące:

* Skonfiguruj EF Core, aby uwzględnić oryginalne wartości kolumn skonfigurowanych jako [tokeny współbieżności](/ef/core/modeling/concurrency) w gdzie klauzuli Update i Delete poleceń.

  Gdy `SaveChanges` jest wywoływana, Where klauzuli wyszukuje oryginalne wartości wszystkich właściwości z adnotacjami z [atrybutem ConcurrenceCheck.](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) Instrukcja aktualizacji nie znajdzie wiersza do aktualizacji, jeśli którykolwiek z właściwości tokenu współbieżności zmienił się od czasu pierwszego odczytu wiersza. EF Core interpretuje to jako konflikt współbieżności. W przypadku tabel bazy danych, które mają wiele kolumn, takie podejście może spowodować bardzo duże klauzule Where i może wymagać dużych ilości stanu. W związku z tym takie podejście nie jest ogólnie zalecane i nie jest to metoda używana w tym samouczku.

* W tabeli bazy danych dołącz kolumnę śledzenia, która może służyć do określenia, kiedy wiersz został zmieniony.

  W bazie danych programu SQL Server typ `rowversion`danych kolumny śledzenia to . Wartość `rowversion` jest numerem sekwencyjnym, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany. W poleceniu Aktualizuj lub Usuń klauzula Where zawiera oryginalną wartość kolumny śledzenia (oryginalny numer wersji wiersza). Jeśli aktualizowany wiersz został zmieniony przez innego użytkownika, wartość w kolumnie `rowversion` jest inna niż oryginalna wartość. W takim przypadku Update lub Delete instrukcji nie można znaleźć wiersz do aktualizacji z powodu Where klauzuli. EF Core zgłasza wyjątek współbieżności, gdy żadne wiersze nie są dotknięte update lub delete polecenia.

## <a name="add-a-tracking-property"></a>Dodawanie właściwości śledzenia

W *models/department.cs*dodaj właściwość śledzenia o nazwie RowVersion:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

[Atrybut sygnatury czasowej](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) jest tym, co identyfikuje kolumnę jako kolumnę śledzenia współbieżności. Płynny interfejs API jest alternatywnym sposobem określenia właściwości śledzenia:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W przypadku bazy danych `[Timestamp]` programu SQL Server atrybut właściwości jednostki zdefiniowanej jako tablica bajtów:

* Powoduje, że kolumna ma być uwzględniona w delete i UPDATE WHERE klauzul.
* Ustawia typ kolumny w bazie danych na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

Baza danych generuje kolejny numer wersji wiersza, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany. W `Update` `Delete` lub polecenia `Where` klauzuli zawiera pobraną wartość wersji wiersza. Jeśli aktualizowany wiersz uległ zmianie od momentu pobrania:

* Bieżąca wartość wersji wiersza nie jest zgodna z pobraną wartością.
* Polecenia `Update` `Delete` lub polecenia nie znajdują wiersza, ponieważ klauzula `Where` wyszukuje pobraną wartość wersji wiersza.
* A `DbUpdateConcurrencyException` jest wyrzucany.

Poniższy kod przedstawia część T-SQL generowane przez EF Core, gdy nazwa działu jest aktualizowana:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Poprzedni wyróżniony kod przedstawia `WHERE` klauzulę `RowVersion`zawierającą . Jeśli baza `RowVersion` danych nie `RowVersion` jest`@p2`równa parametrowi ( ), żadne wiersze nie są aktualizowane.

Poniższy wyróżniony kod pokazuje T-SQL, który weryfikuje dokładnie jeden wiersz został zaktualizowany:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) zwraca liczbę wierszy, których dotyczy ostatnia instrukcja. Jeśli żadne wiersze nie są aktualizowane, EF Core zgłasza . `DbUpdateConcurrencyException`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

W przypadku bazy danych `[Timestamp]` SQLite atrybut właściwości jednostki zdefiniowanej jako tablica bajtów:

* Powoduje, że kolumna ma być uwzględniona w delete i UPDATE WHERE klauzul.
* Mapuje do typu kolumny obiektu BLOB.

Wyzwalacze bazy danych aktualizują kolumnę RowVersion o nową tablicę bajtów losowych za każdym razem, gdy wiersz jest aktualizowany. W `Update` `Delete` lub polecenia `Where` klauzuli zawiera pobraną wartość RowVersion kolumny. Jeśli aktualizowany wiersz uległ zmianie od momentu pobrania:

* Bieżąca wartość wersji wiersza nie jest zgodna z pobraną wartością.
* Polecenie `Update` `Delete` lub nie znajduje wiersza, `Where` ponieważ klauzula wyszukuje oryginalną wartość wersji wiersza.
* A `DbUpdateConcurrencyException` jest wyrzucany.

---

### <a name="update-the-database"></a>Aktualizowanie bazy danych

Dodanie `RowVersion` właściwości zmienia model danych, który wymaga migracji.

Skompiluj projekt. 

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Uruchom następujące polecenie w pmc:

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uruchom następujące polecenie w terminalu:

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

To polecenie:

* Tworzy plik *migracji/{sygnatura czasowa}_RowVersion.cs.*
* Aktualizuje plik *Migrations/SchoolContextModelSnapshot.cs.* Aktualizacja dodaje następujący wyróżniony kod `BuildModel` do metody:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Uruchom następujące polecenie w pmc:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz `Migrations/<timestamp>_RowVersion.cs` plik i dodaj podświetlony kod:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Powyższy kod ma następujące działanie:

  * Aktualizuje istniejące wiersze o losowe wartości obiektów blob.
  * Dodaje wyzwalacze bazy danych, które ustawiają kolumnę RowVersion na losową wartość obiektu blob za każdym razem, gdy wiersz jest aktualizowany.

* Uruchom następujące polecenie w terminalu:

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Strony działu rusztowania

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Postępuj zgodnie z instrukcjami na [stronach Scaffold Student](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:

* Tworzenie folderu *Strony/Działy.*  
* Użyj `Department` dla klasy modelu.
  * Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Tworzenie folderu *Strony/Działy.*

* Uruchom następujące polecenie, aby smikować strony działu.

  **W systemie Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **W systemie Linux lub macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Skompiluj projekt.

## <a name="update-the-index-page"></a>Aktualizowanie strony Indeks

Narzędzie do tworzenia szkieletów `RowVersion` utworzyło kolumnę dla strony Indeks, ale to pole nie będzie wyświetlane w aplikacji produkcyjnej. W tym samouczku ostatni bajt `RowVersion` jest wyświetlany, aby pokazać, jak działa obsługa współbieżności. Ostatni bajt nie jest gwarantowany jako unikatowy sam w sobie.

Strona Aktualizuj *strony\Działy\Index.cshtml:*

* Zamień indeks na działy.
* Zmień kod zawierający, `RowVersion` aby wyświetlić tylko ostatni bajt tablicy bajtowej.
* Zastąp Imię FirstMidname na fullname.

Następujący kod pokazuje zaktualizowaną stronę:

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Aktualizowanie modelu strony Edycja

Aktualizuj *strony\Działy\Edit.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

[OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) jest aktualizowana `rowVersion` o wartość z jednostki, gdy `OnGet` został pobrany w metodzie. EF Core generuje polecenie SQL UPDATE z klauzulą `RowVersion` WHERE zawierającą oryginalną wartość. Jeśli polecenie UPDATE nie ma wpływu na żadne wiersze `RowVersion` (żadne `DbUpdateConcurrencyException` wiersze nie mają oryginalnej wartości), zostanie zgłoszony wyjątek.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

W poprzednim wyróżnionym kodzie:

* Wartość w `Department.RowVersion` jest to, co było w jednostce, gdy został pierwotnie pobrany w Get żądanie dla edytuj strony. Wartość jest dostarczana `OnPost` do metody przez ukryte pole na stronie Razor, które wyświetla encję, która ma być edytowana. Wartość pola ukrytego jest `Department.RowVersion` kopiowana przez spinacza modelu.
* `OriginalValue`jest tym, co EF Core użyje w klauzuli Where. Przed wyróżniony wiersz kodu wykonuje, ma wartość, która była w bazie danych, `OriginalValue` gdy `FirstOrDefaultAsync` został wywołany w tej metodzie, która może się różnić od tego, co zostało wyświetlone na edit strony.
* Wyróżniony kod zapewnia, że EF Core `RowVersion` używa oryginalnej `Department` wartości z wyświetlanej jednostki w instrukcji SQL UPDATE Where klauzuli.

W przypadku wystąpienia błędu współbieżności następujący wyróżniony kod pobiera wartości klienta (wartości zaksięgowane w tej metodzie) i wartości bazy danych.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Poniższy kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych inne niż to, co zostało zaksięgowane do: `OnPostAsync`

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Poniższy wyróżniony kod `RowVersion` ustawia wartość na nową wartość pobraną z bazy danych. Następnym razem, gdy użytkownik kliknie **przycisk Zapisz,** zostaną przechwycone tylko błędy współbieżności, które wystąpią od ostatniego wyświetlenia strony Edytuj.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

Instrukcja `ModelState.Remove` jest wymagana, `ModelState` ponieważ `RowVersion` ma starą wartość. Na stronie Razor `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy oba są obecne.

### <a name="update-the-razor-page"></a>Aktualizowanie strony Razor

Zaktualizuj *strony/działy/edit.cshtml* za pomocą następującego kodu:

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Powyższy kod ma następujące działanie:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.
* Dodaje ukrytą wersję wiersza. `RowVersion`należy dodać, więc zaksięguj z powrotem wiąże wartość.
* Wyświetla ostatni bajt `RowVersion` do celów debugowania.
* `ViewData` Zastępuje silnie wpisane `InstructorNameSL`.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Współbieżność testu powoduje konflikty ze stroną Edytuj

Otwórz dwie przeglądarki wystąpień Edytuj w dziale angielskim:

* Uruchom aplikację i wybierz pozycję Działy.
* Kliknij prawym przyciskiem myszy **hiperłącze Edytuj** dla działu angielskiego i wybierz pozycję Otwórz na **nowej karcie**.
* Na pierwszej karcie kliknij **hiperłącze Edytowanie** dla działu angielskiego.

Dwie karty przeglądarki wyświetlają te same informacje.

Zmień nazwę na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.

![Edycja działu strona 1 po zmianie](concurrency/_static/edit-after-change-130.png)

Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion. Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.

Zmień inne pole na drugiej karcie przeglądarki.

![Edycja działu strona 2 po zmianie](concurrency/_static/edit-after-change-230.png)

Kliknij przycisk **Zapisz**. Są widoczne komunikaty o błędach dla wszystkich pól, które nie są zgodne z wartościami bazy danych:

![Komunikat o błędzie strony edycji działu](concurrency/_static/edit-error30.png)

W tym oknie przeglądarki nie ma zamiaru zmieniać pola Nazwa. Skopiuj i wklej bieżącą wartość (Języki) do pola Nazwa. Wyjmowanie karty. Sprawdzanie poprawności po stronie klienta usuwa komunikat o błędzie.

Kliknij **pozycję Zapisz** ponownie. Wartość wprowadzona na drugiej karcie przeglądarki zostanie zapisana. Zapisane wartości są widoczne na stronie Indeks.

## <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

Zaktualizuj *strony/działy/usuń.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Strona Usuń wykrywa konflikty współbieżności, gdy jednostka została zmieniona po pobraniu. `Department.RowVersion`jest wersją wiersza, gdy jednostka została pobrana. Gdy EF Core tworzy polecenie SQL DELETE, `RowVersion`zawiera klauzulę WHERE z plikiem . Jeśli polecenie SQL DELETE powoduje zero wierszy, których to dotyczy:

* Polecenie `RowVersion` SQL DELETE w programie `RowVersion` SQL nie jest zgodne w bazie danych.
* A DbUpdateConcurrencyException wyjątek jest zgłaszany.
* `OnGetAsync`nazywa się `concurrencyError`z .

### <a name="update-the-delete-razor-page"></a>Aktualizowanie strony Usuń maszynkę do golenia

Zaktualizuj *strony/działy/usuń.cshtml* za pomocą następującego kodu:

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Poprzedni kod wprowadza następujące zmiany:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.
* Dodaje komunikat o błędzie.
* Zastępuje program FirstMidName na fullname w polu **Administrator.**
* Zmiany `RowVersion` w celu wyświetlenia ostatniego bajtu.
* Dodaje ukrytą wersję wiersza. `RowVersion`należy dodać, aby postgit add back powiązać wartość.

### <a name="test-concurrency-conflicts"></a>Testowanie konfliktów współbieżności

Tworzenie działu testowego.

Otwórz dwie przeglądarki wystąpień Delete w dziale testowym:

* Uruchom aplikację i wybierz pozycję Działy.
* Kliknij prawym przyciskiem myszy **usuń** hiperłącze dla działu testowego i wybierz pozycję Otwórz na **nowej karcie**.
* Kliknij **hiperłącze Edytuj** dla działu testowego.

Dwie karty przeglądarki wyświetlają te same informacje.

Zmień budżet na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.

Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion. Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.

Usuń dział testowy z drugiej karty. Błąd współbieżności jest wyświetlany z bieżącymi wartościami z bazy danych. Kliknięcie **przycisku Usuń** powoduje `RowVersion` usunięcie encji, chyba że została usunięta..

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Tokeny współbieżności w EF Core](/ef/core/modeling/concurrency)
* [Obsługa współbieżności w EF Core](/ef/core/saving/concurrency)
* [Debugowanie ASP.NET core 2.x źródło](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Następne kroki

Jest to ostatni samouczek z serii. Dodatkowe tematy są omówione w [wersji MVC z tej serii samouczków](xref:data/ef-mvc/index).

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje jednostkę jednocześnie (w tym samym czasie). Jeśli napotkasz problemy, których nie możesz rozwiązać, [pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pobierz instrukcje](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Konflikty współbieżności

Konflikt współbieżności występuje, gdy:

* Użytkownik przechodzi do strony edycji encji.
* Inny użytkownik aktualizuje tę samą jednostkę przed zapisaniem pierwszej zmiany użytkownika w db.

Jeśli wykrywanie współbieżności nie jest włączone, gdy występują równoczesne aktualizacje:

* Ostatnia aktualizacja wygrywa. Oznacza to, że ostatnie wartości aktualizacji są zapisywane w bazy danych.
* Pierwsza z bieżących aktualizacji zostanie utracona.

### <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Optymistyczna współbieżność umożliwia konflikty współbieżności się zdarzyć, a następnie reaguje odpowiednio, gdy to robią. Na przykład Jane odwiedza stronę edycji departamentu i zmienia budżet działu angielskiego z 350 000,00 USD na 0,00 USD.

![Zmiana budżetu na 0](concurrency/_static/change-budget.png)

Zanim Jane kliknie **przycisk Zapisz,** Jan odwiedza tę samą stronę i zmienia pole Data rozpoczęcia z 1/2007 na 9/1/2013.

![Zmiana daty rozpoczęcia na 2013 r.](concurrency/_static/change-date.png)

Jane klika **najpierw przycisk Zapisz** i widzi jej zmianę, gdy przeglądarka wyświetla stronę Indeks.

![Budżet zmieniony na zero](concurrency/_static/budget-zero.png)

Jan klika **zapisz** na stronie Edycji, która nadal pokazuje budżet 350 000,00 USD. Co dzieje się dalej zależy od sposobu obsługi konfliktów współbieżności.

Optymistyczna współbieżność obejmuje następujące opcje:

* Można śledzić, która właściwość użytkownik zmodyfikował i zaktualizować tylko odpowiednie kolumny w db.

  W scenariuszu żadne dane nie zostaną utracone. Różne właściwości zostały zaktualizowane przez dwóch użytkowników. Następnym razem, gdy ktoś przegląda angielski dział, zobaczą zmiany zarówno Jane, jak i Johna. Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogą spowodować utratę danych. Podejście to:
 
  * Nie można uniknąć utraty danych, jeśli konkurencyjne zmiany są wprowadzane do tej samej właściwości.
  * Ogólnie nie jest praktyczne w aplikacji sieci web. Wymaga utrzymania istotnego stanu w celu śledzenia wszystkich pobranych wartości i nowych wartości. Utrzymywanie dużych ilości stanu może mieć wpływ na wydajność aplikacji.
  * Może zwiększyć złożoność aplikacji w porównaniu do wykrywania współbieżności w jednostce.

* Możesz pozwolić, aby zmiana Johna nadpisyła zmianę Jane.

  Następnym razem, gdy ktoś przegląda dział angielski, zobaczy 9/1/2013 i pobraną wartość $350,000.00. Takie podejście jest nazywany *klient wins* lub *Ostatni w* wins scenariusza. (Wszystkie wartości od klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jeśli nie wykonasz żadnego kodowania do obsługi współbieżności, wygrywa klient odbywa się automatycznie.

* Można zapobiec zmiany Jana z aktualizowaniem w db. Zazwyczaj aplikacja będzie:

  * Wyświetl komunikat o błędzie.
  * Pokaż bieżący stan danych.
  * Zezwalaj użytkownikowi na ponowne zastosowanie zmian.

  Jest to tak zwany scenariusz *Wygrane sklepu.* (Wartości magazynu danych mają pierwszeństwo przed wartościami przesłane przez klienta). Zaimplementowanie scenariusza Wygrane sklepu w tym samouczku. Ta metoda gwarantuje, że żadne zmiany nie są zastępowane bez użytkownika jest alerty.

## <a name="handling-concurrency"></a>Obsługa współbieżności 

Gdy właściwość jest skonfigurowana jako [token współbieżności:](/ef/core/modeling/concurrency)

* EF Core sprawdza, że właściwość nie została zmodyfikowana po pobraniu. Sprawdzanie występuje, gdy [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) lub [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) jest wywoływana.
* Jeśli właściwość została zmieniona po pobraniu, [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) jest generowany. 

Baza danych i model danych muszą być `DbUpdateConcurrencyException`skonfigurowane do obsługi rzucania .

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Wykrywanie konfliktów współbieżności we właściwości

Konflikty współbieżności można wykryć na poziomie właściwości za pomocą [atrybutu ConcurrencyCheck.](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) Atrybut można zastosować do wielu właściwości modelu. Aby uzyskać więcej informacji, zobacz [Annotacje danych-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

Atrybut `[ConcurrencyCheck]` nie jest używany w tym samouczku.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Wykrywanie konfliktów współbieżności w wierszu

Aby wykryć konflikty współbieżności, kolumna śledzenia [wierszy](/sql/t-sql/data-types/rowversion-transact-sql) jest dodawany do modelu.  `rowversion` :

* Czy sql server specyficzne. Inne bazy danych mogą nie zapewniać podobnej funkcji.
* Służy do określenia, że jednostka nie została zmieniona, ponieważ została pobrana z bazy danych. 

Baza danych generuje `rowversion` kolejny numer, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany. W `Update` poleceniu lub `Delete` w poleceniu `Where` `rowversion`klauzula zawiera pobraną wartość . Jeśli zaktualizowany wiersz uległ zmianie:

* `rowversion`nie pasuje do pobranej wartości.
* Polecenia `Update` `Delete` lub polecenia nie znajdują wiersza, ponieważ `Where` klauzula `rowversion`zawiera pobrane .
* A `DbUpdateConcurrencyException` jest wyrzucany.

W EF Core, gdy żadne wiersze `Update` `Delete` nie zostały zaktualizowane przez polecenie lub, wyjątek współbieżności jest zgłaszany.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Dodawanie właściwości śledzenia do encji Dział

W *models/department.cs*dodaj właściwość śledzenia o nazwie RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

[Atrybut sygnatury czasowej](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) określa, że `Where` ta `Update` kolumna znajduje się w klauzuli i `Delete` polecenia. Atrybut jest wywoływany, `Timestamp` ponieważ poprzednie wersje `timestamp` programu SQL Server `rowversion` używały typu danych SQL, zanim typ SQL zastąpił go.

Płynny interfejs API można również określić właściwości śledzenia:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Poniższy kod przedstawia część T-SQL generowane przez EF Core, gdy nazwa działu jest aktualizowana:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Poprzedni wyróżniony kod przedstawia `WHERE` klauzulę `RowVersion`zawierającą . Jeśli baza `RowVersion` danych nie jest `RowVersion` równa`@p2`parametrowi ( ), żadne wiersze nie są aktualizowane.

Poniższy wyróżniony kod pokazuje T-SQL, który weryfikuje dokładnie jeden wiersz został zaktualizowany:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) zwraca liczbę wierszy, których dotyczy ostatnia instrukcja. W żadnym wierszu nie są aktualizowane, EF Core rzuca . `DbUpdateConcurrencyException`

Można zobaczyć T-SQL EF Core generuje w oknie danych wyjściowych programu Visual Studio.

### <a name="update-the-db"></a>Aktualizowanie bazy danych

Dodanie `RowVersion` właściwości zmienia model bazy danych, który wymaga migracji.

Skompiluj projekt. W oknie polecenia wprowadź następujące elementy:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Poprzednie polecenia:

* Dodaje plik *migracji/{sygnatura czasowa}_RowVersion.cs.*
* Aktualizuje plik *Migrations/SchoolContextModelSnapshot.cs.* Aktualizacja dodaje następujący wyróżniony kod `BuildModel` do metody:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Uruchamia migracje, aby zaktualizować bazę danych.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Rusztowanie modelu działów

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio) 

Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-student-pages) i używać `Department` dla klasy modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Uruchom następujące polecenie:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Poprzednie polecenie scaffolds `Department` modelu. Otwórz projekt w programie Visual Studio.

Skompiluj projekt.

### <a name="update-the-departments-index-page"></a>Aktualizowanie strony Indeks działów

Aparat szkieletu utworzył kolumnę `RowVersion` dla strony Indeks, ale to pole nie powinno być wyświetlane. W tym samouczku ostatni bajt `RowVersion` jest wyświetlany, aby ułatwić zrozumienie współbieżności. Ostatni bajt nie jest gwarantowany jako unikatowy. Prawdziwa aplikacja nie będzie `RowVersion` wyświetlana lub `RowVersion`ostatni bajt .

Zaktualizuj stronę Indeks:

* Zamień indeks na działy.
* Zastąp znaczniki zawierające `RowVersion` ostatni `RowVersion`bajt .
* Zastąp Imię FirstMidname na fullname.

Zaktualizowana strona pokazuje zaktualizowaną stronę:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualizowanie modelu strony Edycja

Aktualizuj *strony\Działy\Edit.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Aby wykryć problem współbieżności, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) `rowVersion` jest aktualizowany o wartość z jednostki, która została pobrana. EF Core generuje polecenie SQL UPDATE z klauzulą `RowVersion` WHERE zawierającą oryginalną wartość. Jeśli polecenie UPDATE nie ma wpływu na żadne wiersze `RowVersion` (żadne `DbUpdateConcurrencyException` wiersze nie mają oryginalnej wartości), zostanie zgłoszony wyjątek.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

W poprzednim kodzie `Department.RowVersion` jest wartość, gdy jednostka została pobrana. `OriginalValue`jest wartością w DB, gdy `FirstOrDefaultAsync` został wywołany w tej metodzie.

Następujący kod pobiera wartości klienta (wartości zaksięgowane w tej metodzie) i wartości bazy danych:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Poniższy kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, `OnPostAsync`która ma wartości bazy danych różni się od tego, co zostało zaksięgowane do:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Poniższy wyróżniony kod `RowVersion` ustawia wartość na nową wartość pobraną z bazy danych. Następnym razem, gdy użytkownik kliknie **przycisk Zapisz,** zostaną przechwycone tylko błędy współbieżności, które wystąpią od ostatniego wyświetlenia strony Edytuj.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Instrukcja `ModelState.Remove` jest wymagana, `ModelState` ponieważ `RowVersion` ma starą wartość. Na stronie Razor `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy oba są obecne.

## <a name="update-the-edit-page"></a>Aktualizowanie strony edycji

Zaktualizuj *strony/działy/edit.cshtml* za pomocą następujących znaczników:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Powyższe znaczniki:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.
* Dodaje ukrytą wersję wiersza. `RowVersion`należy dodać, więc zaksięguj z powrotem wiąże wartość.
* Wyświetla ostatni bajt `RowVersion` do celów debugowania.
* `ViewData` Zastępuje silnie wpisane `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Współbieżność testu powoduje konflikty ze stroną Edytuj

Otwórz dwie przeglądarki wystąpień Edytuj w dziale angielskim:

* Uruchom aplikację i wybierz pozycję Działy.
* Kliknij prawym przyciskiem myszy **hiperłącze Edytuj** dla działu angielskiego i wybierz pozycję Otwórz na **nowej karcie**.
* Na pierwszej karcie kliknij **hiperłącze Edytowanie** dla działu angielskiego.

Dwie karty przeglądarki wyświetlają te same informacje.

Zmień nazwę na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.

![Edycja działu strona 1 po zmianie](concurrency/_static/edit-after-change-1.png)

Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion. Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.

Zmień inne pole na drugiej karcie przeglądarki.

![Edycja działu strona 2 po zmianie](concurrency/_static/edit-after-change-2.png)

Kliknij przycisk **Zapisz**. Są widoczne komunikaty o błędach dla wszystkich pól, które nie są zgodne z wartościami bazy danych:

![Komunikat o błędzie strony edycji działu](concurrency/_static/edit-error.png)

W tym oknie przeglądarki nie ma zamiaru zmieniać pola Nazwa. Skopiuj i wklej bieżącą wartość (Języki) do pola Nazwa. Wyjmowanie karty. Sprawdzanie poprawności po stronie klienta usuwa komunikat o błędzie.

![Komunikat o błędzie strony edycji działu](concurrency/_static/cv.png)

Kliknij **pozycję Zapisz** ponownie. Wartość wprowadzona na drugiej karcie przeglądarki zostanie zapisana. Zapisane wartości są widoczne na stronie Indeks.

## <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

Zaktualizuj model strony Usuń o następujący kod:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Strona Usuń wykrywa konflikty współbieżności, gdy jednostka została zmieniona po pobraniu. `Department.RowVersion`jest wersją wiersza, gdy jednostka została pobrana. Gdy EF Core tworzy polecenie SQL DELETE, `RowVersion`zawiera klauzulę WHERE z plikiem . Jeśli polecenie SQL DELETE powoduje zero wierszy, których to dotyczy:

* Polecenie `RowVersion` SQL DELETE nie jest `RowVersion` zgodne w bazy danych.
* A DbUpdateConcurrencyException wyjątek jest zgłaszany.
* `OnGetAsync`nazywa się `concurrencyError`z .

### <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

Zaktualizuj *strony/działy/usuń.cshtml* za pomocą następującego kodu:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Poprzedni kod wprowadza następujące zmiany:

* Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.
* Dodaje komunikat o błędzie.
* Zastępuje program FirstMidName na fullname w polu **Administrator.**
* Zmiany `RowVersion` w celu wyświetlenia ostatniego bajtu.
* Dodaje ukrytą wersję wiersza. `RowVersion`należy dodać, więc zaksięguj z powrotem wiąże wartość.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Współbieżność testu powoduje konflikty ze stroną Usuwanie

Tworzenie działu testowego.

Otwórz dwie przeglądarki wystąpień Delete w dziale testowym:

* Uruchom aplikację i wybierz pozycję Działy.
* Kliknij prawym przyciskiem myszy **usuń** hiperłącze dla działu testowego i wybierz pozycję Otwórz na **nowej karcie**.
* Kliknij **hiperłącze Edytuj** dla działu testowego.

Dwie karty przeglądarki wyświetlają te same informacje.

Zmień budżet na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.

Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion. Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.

Usuń dział testowy z drugiej karty. Błąd współbieżności jest wyświetlany z bieżącymi wartościami z bazy danych. Kliknięcie **przycisku Usuń** powoduje `RowVersion` usunięcie encji, chyba że została usunięta..

Zobacz [Dziedziczenie,](xref:data/ef-mvc/inheritance) jak dziedziczyć model danych.

### <a name="additional-resources"></a>Zasoby dodatkowe

* [Tokeny współbieżności w EF Core](/ef/core/modeling/concurrency)
* [Obsługa współbieżności w EF Core](/ef/core/saving/concurrency)
* [Wersja YouTube tego samouczka(Obsługa konfliktów współbieżności)](https://youtu.be/EosxHTFgYps)
* [Wersja YouTube tego samouczka (część 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Wersja YouTube tego samouczka(Część 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Wstecz](xref:data/ef-rp/update-related-data)

::: moniker-end

