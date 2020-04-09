---
title: 'Samouczek: Dowiedz się więcej o zaawansowanych scenariuszach - ASP.NET MVC z EF Core'
description: W tym samouczku przedstawiono przydatne tematy wykraczające poza podstawy tworzenia aplikacji sieci web ASP.NET Core, które używają programu Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: fc6f8d8c4ab09848cf316be2e522bf5ce3b9ac76
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416234"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Samouczek: Dowiedz się więcej o zaawansowanych scenariuszach - ASP.NET MVC z EF Core

W poprzednim samouczku zaimplementowano dziedziczenie tabeli na hierarchię. W tym samouczku przedstawiono kilka tematów, które są przydatne, aby być świadomym, gdy wykracza poza podstawy tworzenia ASP.NET podstawowych aplikacji sieci web, które używają Entity Framework Core.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Wykonywanie nieprzetworzonych zapytań SQL
> * Wywoływanie kwerendy w celu zwrócenia encji
> * Wywoływanie kwerendy w celu zwrócenia innych typów
> * Wywoływanie kwerendy aktualizującej
> * Badanie zapytań SQL
> * Tworzenie warstwy abstrakcji
> * Dowiedz się więcej o automatycznym wykrywaniu zmian
> * Dowiedz się więcej o kodzie źródłowym EF Core i planach rozwoju
> * Dowiedz się, jak używać dynamicznego linq w celu uproszczenia kodu

## <a name="prerequisites"></a>Wymagania wstępne

* [Implementowanie dziedziczenia](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Wykonywanie nieprzetworzonych zapytań SQL

Jedną z zalet korzystania z entity framework jest, że unika wiązania kodu zbyt ściśle do określonej metody przechowywania danych. Czyni to poprzez generowanie zapytań SQL i poleceń dla Ciebie, co również uwalnia od konieczności pisania ich samodzielnie. Istnieją jednak wyjątkowe scenariusze, gdy trzeba uruchomić określone zapytania SQL, które zostały utworzone ręcznie. W tych scenariuszach interfejs API code framework framework zawiera metody, które umożliwiają przekazywanie poleceń SQL bezpośrednio do bazy danych. W EF Core 1.0 dostępne są następujące opcje:

* Użyj `DbSet.FromSql` metody dla kwerend, które zwracają typy jednostek. Zwrócone obiekty muszą być typu `DbSet` oczekiwanego przez obiekt i są automatycznie śledzone przez kontekst bazy danych, chyba że [zostanie wyłączone śledzenie](crud.md#no-tracking-queries).

* Użyj `Database.ExecuteSqlCommand` dla poleceń innych niż kwerendy.

Jeśli musisz uruchomić kwerendę, która zwraca typy, które nie są jednostkami, można użyć ADO.NET z połączeniem bazy danych dostarczonym przez EF. Zwrócone dane nie są śledzone przez kontekst bazy danych, nawet jeśli ta metoda jest używana do pobierania typów jednostek.

Jak zawsze podczas wykonywania poleceń SQL w aplikacji sieci web, należy podjąć środki ostrożności, aby chronić witrynę przed atakami iniekcji SQL. Jednym ze sposobów, aby to zrobić, jest użycie sparametryzowanych zapytań, aby upewnić się, że ciągi przesyłane przez stronę sieci web nie mogą być interpretowane jako polecenia SQL. W tym samouczku użyjesz sparametryzowanych zapytań podczas integrowania danych wejściowych użytkownika do kwerendy.

## <a name="call-a-query-to-return-entities"></a>Wywoływanie kwerendy w celu zwrócenia encji

Klasa `DbSet<TEntity>` zawiera metodę, której można użyć do wykonania kwerendy `TEntity`zwracanej jednostki typu . Aby zobaczyć, jak to działa, zmienisz kod w metodzie `Details` kontrolera działu.

W *DepartmentsController.cs*, w `Details` metodzie, zastąp kod, `FromSql` który pobiera dział wywołaniem metody, jak pokazano w poniższym wyróżnionym kodem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Aby sprawdzić, czy nowy kod działa poprawnie, wybierz kartę **Działy,** a następnie **pozycję Szczegóły** dla jednego z działów.

![Szczegóły działu](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Wywoływanie kwerendy w celu zwrócenia innych typów

Wcześniej utworzono siatkę statystyk uczniów dla strony Informacje, która pokazywała liczbę uczniów dla każdej daty rejestracji. Masz dane z zestawu jednostek`_context.Students`Studenci ( ) i używane LINQ `EnrollmentDateGroup` do projektu wyników na liście obiektów modelu widoku. Załóżmy, że chcesz napisać sam SQL, a nie przy użyciu LINQ. Aby to zrobić, należy uruchomić kwerendę SQL, która zwraca coś innego niż obiekty jednostki. W EF Core 1.0 jednym ze sposobów, aby to zrobić, jest napisać ADO.NET kodu i uzyskać połączenie z bazą danych z EF.

W *HomeController.cs*, zastąp `About` metodę następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Dodaj instrukcję using:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Uruchom aplikację i przejdź do strony Informacje. Wyświetla te same dane, co wcześniej.

![Strona Informacje](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Wywoływanie kwerendy aktualizującej

Załóżmy, że administratorzy contoso university chcą wykonywać globalne zmiany w bazie danych, takie jak zmiana liczby kredytów dla każdego kursu. Jeśli uniwersytet ma dużą liczbę kursów, byłoby nieefektywne, aby pobrać je wszystkie jako podmioty i zmienić je indywidualnie. W tej sekcji zaimplementujesz stronę sieci web, która umożliwia użytkownikowi określenie współczynnika, za pomocą którego można zmienić liczbę kredytów dla wszystkich kursów, a zmienisz ją, wykonując instrukcję SQL UPDATE. Strona internetowa będzie wyglądać następująco:

![Strona Aktualizowanie kredytów kursu](advanced/_static/update-credits.png)

W *CoursesController.cs*, dodaj Metody UpdateCourseCredits dla HttpGet i HttpPost:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Gdy kontroler przetwarza żądanie HttpGet, nic `ViewData["RowsAffected"]`nie jest zwracany w , a widok wyświetla puste pole tekstowe i przycisk prześlij, jak pokazano na poprzedniej ilustracji.

Po kliknięciu przycisku **Aktualizuj** wywoływana jest metoda HttpPost, a mnożnik zawiera wartość wpisana w polu tekstowym. Następnie kod wykonuje sql, który aktualizuje kursy i zwraca liczbę wierszy, których dotyczy problem, do widoku w `ViewData`programie . Gdy widok pobiera `RowsAffected` wartość, wyświetla liczbę zaktualizowanych wierszy.

W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Widoki/Kursy,* a następnie kliknij polecenie **Dodaj > Nowy element**.

W oknie dialogowym **Dodawanie nowego elementu** kliknij pozycję ASP.NET **Core** w obszarze **Zainstalowane** w lewym okienku, kliknij pozycję **Razor View**i nazwij nowy widok *UpdateCourseCredits.cshtml*.

W *views/courses/updatecoursecredits.cshtml*, zastąp kod szablonu następującym kodem:

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Uruchom `UpdateCourseCredits` metodę, wybierając kartę **Kursy,** a następnie dodając "/UpdateCourseCredits" na końcu adresu URL na pasku adresu przeglądarki (na przykład: `http://localhost:5813/Courses/UpdateCourseCredits`). Wprowadź numer w polu tekstowym:

![Strona Aktualizowanie kredytów kursu](advanced/_static/update-credits.png)

Kliknij przycisk **Update** (Aktualizuj). Widzisz liczbę wierszy, których to dotyczy:

![Dotyczy wierszy strony Aktualizowanie kredytów kursu](advanced/_static/update-credits-rows-affected.png)

Kliknij **przycisk Wstecz do listy,** aby wyświetlić listę kursów ze zmienioną liczbą kredytów.

Należy zauważyć, że kod produkcyjny zapewni, że aktualizacje zawsze powodują prawidłowe dane. Podany tutaj uproszczony kod może pomnożyć liczbę kredytów na tyle, aby zwiększyć liczbę większą niż 5. (Właściwość `Credits` ma `[Range(0, 5)]` atrybut.) Kwerenda aktualizująca będzie działać, ale nieprawidłowe dane mogą powodować nieoczekiwane wyniki w innych częściach systemu, które zakładają, że liczba kredytów wynosi 5 lub mniej.

Aby uzyskać więcej informacji na temat nieprzetworzonych zapytań SQL, zobacz [Nieprzetworzone kwerendy SQL](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Badanie zapytań SQL

Czasami jest to przydatne, aby móc zobaczyć rzeczywiste zapytania SQL, które są wysyłane do bazy danych. Wbudowana funkcja rejestrowania dla ASP.NET Core jest automatycznie używana przez EF Core do pisania dzienników zawierających sql dla zapytań i aktualizacji. W tej sekcji zobaczysz kilka przykładów rejestrowania SQL.

Otwórz *StudentsController.cs* i w `Details` metodzie ustawić punkt `if (student == null)` przerwania na instrukcji.

Uruchom aplikację w trybie debugowania i przejdź do strony Szczegóły dla ucznia.

Przejdź do okna **Dane wyjściowe** z wyświetlonymi wyjściami debugowania, a zostanie wyświetloną kwerenda:

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

Zauważysz tutaj coś, co może Cię zaskoczyć: SQL wybiera`TOP(2)`maksymalnie 2 wiersze ( ) z tabeli Osoba. Metoda `SingleOrDefaultAsync` nie rozpoznać do 1 wiersza na serwerze. Oto dlaczego:

* Jeśli kwerenda zwróci wiele wierszy, metoda zwraca wartość null.
* Aby ustalić, czy kwerenda zwróci wiele wierszy, EF musi sprawdzić, czy zwraca co najmniej 2.

Należy zauważyć, że nie trzeba używać trybu debugowania i zatrzymać w punkcie przerwania, aby uzyskać dane wyjściowe rejestrowania w oknie **Dane wyjściowe.** Jest to po prostu wygodny sposób, aby zatrzymać rejestrowanie w punkcie, który chcesz spojrzeć na dane wyjściowe. Jeśli tego nie zrobisz, rejestrowanie jest kontynuowane i musisz przewinąć do tyłu, aby znaleźć interesujące Cię części.

## <a name="create-an-abstraction-layer"></a>Tworzenie warstwy abstrakcji

Wielu deweloperów napisać kod do zaimplementowania repozytorium i jednostki wzorców pracy jako otoki wokół kodu, który współpracuje z Entity Framework. Te wzorce są przeznaczone do tworzenia warstwy abstrakcji między warstwą dostępu do danych a warstwą logiki biznesowej aplikacji. Implementowanie tych wzorców może pomóc izolować aplikację od zmian w magazynie danych i może ułatwić automatyczne testowanie jednostek lub programowanie oparte na testach (TDD). Jednak pisanie dodatkowego kodu w celu zaimplementowania tych wzorców nie zawsze jest najlepszym wyborem dla aplikacji korzystających z EF, z kilku powodów:

* Klasa kontekstu EF sama izoluje kod z kodu specyficznego dla magazynu danych.

* Klasa kontekstu EF może działać jako klasa jednostki pracy dla aktualizacji bazy danych, które można wykonać przy użyciu EF.

* EF zawiera funkcje implementacji TDD bez pisania kodu repozytorium.

Aby uzyskać informacje na temat implementacji repozytorium i jednostki wzorców pracy, zobacz [Entity Framework 5 wersja tej serii samouczków](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implementuje dostawcę bazy danych w pamięci, który może służyć do testowania. Aby uzyskać więcej informacji, zobacz [Testowanie za pomocą usługi InMemory](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Automatyczne wykrywanie zmian

Entity Framework określa, jak jednostka została zmieniona (i w związku z tym, które aktualizacje muszą być wysyłane do bazy danych) przez porównanie bieżących wartości jednostki z oryginalnymi wartościami. Oryginalne wartości są przechowywane, gdy jednostka jest wyszukiwana lub dołączona. Niektóre metody, które powodują automatyczne wykrywanie zmian są następujące:

* DbContext.SaveZmienia się

* DbContext.Entry

* Pozycje ChangeTracker.Entries

Jeśli śledzisz dużą liczbę jednostek i dzwonisz do jednej z tych metod wiele razy w pętli, możesz uzyskać znaczną poprawę wydajności, tymczasowo wyłączając automatyczne wykrywanie zmian przy użyciu `ChangeTracker.AutoDetectChangesEnabled` właściwości. Przykład:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>Ef Podstawowy kod źródłowy i plany rozwoju

Źródło core programu Entity [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)Framework znajduje się na poziomie . Repozytorium EF Core zawiera kompilacje nocne, śledzenie problemów, specyfikacje funkcji, notatki ze spotkania projektowego i [plan rozwoju w przyszłości.](https://github.com/dotnet/efcore/wiki/Roadmap) Możesz zgłosić lub znaleźć błędy i przyczynić się.

Mimo że kod źródłowy jest otwarty, Entity Framework Core jest w pełni obsługiwany jako produkt firmy Microsoft. Zespół microsoft entity framework zachowuje kontrolę nad tym, które wkłady są akceptowane i testuje wszystkie zmiany kodu, aby zapewnić jakość każdej wersji.

## <a name="reverse-engineer-from-existing-database"></a>Inżynier odwrotny z istniejącej bazy danych

Aby odtworzyć model danych, w tym klasy jednostek z istniejącej bazy danych, należy użyć polecenia [szkieletu-dbcontext.](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) Zobacz [samouczek wprowadzenie](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Użyj dynamicznego LINQ, aby uprościć kod

[Trzeci samouczek z tej serii](sort-filter-page.md) pokazuje, jak napisać kod LINQ przez hard-coding nazwy kolumn w instrukcji. `switch` Z dwóch kolumn do wyboru, to działa dobrze, ale jeśli masz wiele kolumn kod może uzyskać pełne. Aby rozwiązać ten problem, `EF.Property` można użyć metody, aby określić nazwę właściwości jako ciąg. Aby wypróbować to podejście, `Index` zastąp metodę w poniższym `StudentsController` kodzie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Podziękowania

Tom Dykstra i Rick @RickAndMSFTAnderson (twitter) napisał ten poradnik. Rowan Miller, Diego Vega i inni członkowie zespołu Entity Framework pomagali w przeglądaniu kodu i pomagali w debugowaniu problemów, które pojawiły się podczas pisania kodu dla samouczków. John Parente i Paul Goldman pracowali nad aktualizacją samouczka dla ASP.NET Core 2.2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Rozwiązywanie typowych problemów

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll używany przez inny proces

Komunikat o błędzie:

> Nie można otworzyć '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' do zapisu -- "Proces nie może uzyskać dostępu do pliku '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll', ponieważ jest on używany przez inny proces.

Rozwiązanie:

Zatrzymaj witrynę w układzie IIS Express. Przejdź do zasobnika systemowego systemu Windows, znajdź program IIS Express i kliknij prawym przyciskiem myszy jego ikonę, wybierz witrynę Uniwersytetu Contoso, a następnie kliknij pozycję **Zatrzymaj witrynę**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Szkielety migracji bez kodu w metodach w górę i w dół

Możliwa przyczyna:

Polecenia EF CLI nie zamykają automatycznie i nie zapisują plików kodu. Jeśli po uruchomieniu `migrations add` polecenia wprowadzono niezapisane zmiany, ef nie znajdzie zmian.

Rozwiązanie:

Uruchom `migrations remove` polecenie, zapisz zmiany kodu i `migrations add` uruchom ponownie polecenie.

### <a name="errors-while-running-database-update"></a>Błędy podczas uruchamiania aktualizacji bazy danych

Istnieje możliwość uzyskania innych błędów podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane. Jeśli widzisz błędy migracji, których nie można rozwiązać, możesz zmienić nazwę bazy danych w ciągu połączenia lub usunąć bazę danych. W nowej bazie danych nie ma żadnych danych do migracji, a polecenie update-database jest znacznie bardziej prawdopodobne, aby zakończyć bez błędów.

Najprostszym podejściem jest zmiana nazwy bazy danych w *appsettings.json*. Przy następnym uruchomieniu `database update`zostanie utworzona nowa baza danych.

Aby usunąć bazę danych w SSOX, kliknij prawym przyciskiem myszy bazę danych, kliknij polecenie **Usuń**, a następnie w oknie dialogowym **Usuwanie bazy danych** wybierz pozycję Zamknij istniejące **połączenia** i kliknij przycisk **OK**.

Aby usunąć bazę danych przy `database drop` użyciu interfejsu wiersza polecenia, uruchom polecenie CLI:

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Błąd podczas lokalizowania wystąpienia programu SQL Server

Komunikat o błędzie:

> Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (dostawca: interfejsy sieciowe SQL, błąd: 26 - Określono błąd lokalizowania serwera/wystąpienia)

Rozwiązanie:

Sprawdź parametry połączenia. Jeśli plik bazy danych został ręcznie usunięty, zmień nazwę bazy danych w ciągu budowy, aby rozpocząć od nowa z nową bazą danych.

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji na temat EF Core, zobacz [dokumentację Entity Framework Core](/ef/core). Dostępna jest również książka: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).

Aby uzyskać informacje na temat wdrażania <xref:host-and-deploy/index>aplikacji sieci web, zobacz .

Aby uzyskać informacje na temat innych tematów związanych z ASP.NET <xref:index>Core MVC, takich jak uwierzytelnianie i autoryzacja, zobacz .

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Wykonywane nieprzetworzone kwerendy SQL
> * Wywoływane zapytanie do zwracania encji
> * Wywołana kwerenda w celu zwrócenia innych typów
> * Wywoływana kwerenda aktualizująca
> * Zbadane kwerendy SQL
> * Utworzono warstwę abstrakcyjną
> * Dowiedz się więcej o automatycznym wykrywaniu zmian
> * Dowiedz się więcej o kodzie źródłowym EF Core i planach rozwoju
> * Dowiedz się, jak używać dynamicznego LINQ w celu uproszczenia kodu

Spowoduje to ukończenie tej serii samouczków dotyczących korzystania z rdzenia entity framework w aplikacji ASP.NET Core MVC. Ta seria pracowała z nową bazą danych; alternatywą jest odtwolinie modelu z istniejącej bazy danych.

> [!div class="nextstepaction"]
> [Samouczek: EF Core z MVC, istniejąca baza danych](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
