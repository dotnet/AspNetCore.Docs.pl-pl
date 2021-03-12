---
title: 'Samouczek: informacje na temat scenariuszy zaawansowanych — ASP.NET MVC z EF Core'
description: W tym samouczku przedstawiono przydatne tematy umożliwiające przechodzenie poza podstawowe informacje na temat tworzenia ASP.NET Core aplikacji sieci Web korzystających z Entity Framework Core.
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 135881bfb4137bc81bd0f7250f1ce191cab13dcd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586842"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Samouczek: informacje na temat scenariuszy zaawansowanych — ASP.NET MVC z EF Core

W poprzednim samouczku zaimplementowano dziedziczenie w hierarchii na poziomie tabeli. W tym samouczku przedstawiono kilka tematów, które są przydatne, gdy wykraczasz poza podstawowe informacje na temat tworzenia ASP.NET Core aplikacji sieci Web korzystających z Entity Framework Core.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Wykonywanie nieprzetworzonych zapytań SQL
> * Wywoływanie zapytania w celu zwrócenia jednostek
> * Wywoływanie zapytania w celu zwrócenia innych typów
> * Wywoływanie zapytania Update
> * Badanie zapytań SQL
> * Tworzenie warstwy abstrakcji
> * Informacje o automatycznym wykrywaniu zmian
> * Dowiedz się więcej na temat EF Core kodu źródłowego i planów programistycznych
> * Dowiedz się, jak używać dynamicznego LINQ do uproszczenia kodu

## <a name="prerequisites"></a>Wymagania wstępne

* [Implementowanie dziedziczenia](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Wykonywanie nieprzetworzonych zapytań SQL

Jedną z zalet korzystania z Entity Framework jest to, że pozwala to uniknąć zbyt ścisłego powiązana kodu z konkretną metodą przechowywania danych. Robi to przez generowanie zapytań i poleceń SQL, które również uwalniają użytkownika od konieczności pisania. Ale istnieją wyjątkowe scenariusze, które należy wykonać w celu uruchomienia określonych zapytań SQL, które zostały utworzone ręcznie. W tych scenariuszach Entity Framework interfejs API Code First zawiera metody umożliwiające przekazywanie poleceń SQL bezpośrednio do bazy danych. Dostępne są następujące opcje w EF Core 1,0:

* Użyj `DbSet.FromSql` metody dla zapytań, które zwracają typy jednostek. Zwracane obiekty muszą być typu oczekiwanego przez `DbSet` obiekt i są automatycznie śledzone przez kontekst bazy danych, chyba że zostanie [wyłączone śledzenie](crud.md#no-tracking-queries).

* Użyj `Database.ExecuteSqlCommand` polecenia dla poleceń niezwiązanych z kwerendą.

Jeśli musisz uruchomić zapytanie, które zwraca typy, które nie są jednostkami, możesz użyć ADO.NET z połączeniem bazy danych udostępnionym przez EF. Zwrócone dane nie są śledzone przez kontekst bazy danych, nawet jeśli ta metoda jest używana do pobierania typów jednostek.

Gdy jest zawsze prawdziwe w przypadku wykonywania poleceń SQL w aplikacji sieci Web, należy podjąć środki ostrożności, aby chronić witrynę przed atakami polegającymi na iniekcji SQL. Jednym ze sposobów jest użycie zapytań parametrycznych w celu upewnienia się, że ciągi przesłane przez stronę sieci Web nie mogą być interpretowane jako polecenia SQL. W tym samouczku użyjesz zapytań parametrycznych podczas integrowania danych wejściowych użytkownika z zapytaniem.

## <a name="call-a-query-to-return-entities"></a>Wywoływanie zapytania w celu zwrócenia jednostek

`DbSet<TEntity>`Klasa zawiera metodę, której można użyć do wykonania zapytania zwracającego jednostkę typu `TEntity` . Aby zobaczyć, jak to działa, Zmień kod w `Details` metodzie kontrolera działu.

W *DepartmentsController.cs*, w `Details` metodzie, Zastąp kod pobierający dział z `FromSql` wywołaniem metody, jak pokazano w następującym wyróżnionym kodzie:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Aby sprawdzić, czy nowy kod działa prawidłowo, wybierz kartę **działy** , a następnie **szczegóły** dla jednego z działów.

![Szczegóły działu](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Wywoływanie zapytania w celu zwrócenia innych typów

Wcześniej utworzono siatkę statystyk uczniów dla strony informacje, która wykazała liczbę studentów dla każdej daty rejestracji. Uzyskano dane z zestawu jednostek studentów ( `_context.Students` ) i używane LINQ do projekcji wyników do listy `EnrollmentDateGroup` obiektów modelu widoku. Załóżmy, że chcesz napisać sam kod SQL, zamiast używać LINQ. W tym celu należy uruchomić zapytanie SQL zwracające coś innego niż obiekty Entity. W EF Core 1,0 jednym ze sposobów jest zapisanie kodu ADO.NET i nawiązanie połączenia z bazą danych EF.

W *HomeController.cs* Zastąp `About` metodę następującym kodem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Dodaj instrukcję using:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Uruchom aplikację i przejdź do strony informacje. Wyświetla te same dane, które wcześniej zaistniały.

![Informacje o stronie](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Wywoływanie zapytania Update

Załóżmy, że administratorzy uniwersytetów firmy Contoso chcą wykonywać globalne zmiany w bazie danych, na przykład zmieniając liczbę kredytów dla każdego kursu. Jeśli University ma dużą liczbę kursów, może być nieefektywny do pobrania ich wszystkie jako jednostki i indywidualnie zmienić. W tej sekcji zostanie zaimplementowana Strona sieci Web, która umożliwia użytkownikowi określenie współczynnika, przez który należy zmienić liczbę kredytów dla wszystkich kursów, i wprowadzić zmiany przez wykonanie instrukcji SQL UPDATE. Strona sieci Web będzie wyglądać jak na poniższej ilustracji:

![Strona aktualizacji kredytów kursu](advanced/_static/update-credits.png)

W *CoursesController.cs* Dodaj metody UpdateCourseCredits dla narzędzia HttpGet i HTTPPOST:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Gdy kontroler przetwarza żądanie narzędzia HttpGet, nic nie jest zwracane w `ViewData["RowsAffected"]` , a widok wyświetla puste pole tekstowe i przycisk Prześlij, jak pokazano na poprzedniej ilustracji.

Po kliknięciu przycisku **Aktualizuj** Metoda HTTPPOST jest wywoływana, a mnożnik ma wartość wprowadzoną w polu tekstowym. Następnie kod wykonuje instrukcję SQL, która aktualizuje kursy i zwraca liczbę odnośnych wierszy do widoku w `ViewData` . Gdy widok pobiera `RowsAffected` wartość, wyświetlana jest liczba zaktualizowanych wierszy.

W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *widoki/kursy* , a następnie kliknij polecenie **Dodaj > nowy element**.

W oknie dialogowym **Dodaj nowy element** kliknij **ASP.NET Core** w obszarze **zainstalowane** w okienku po lewej stronie, kliknij pozycję **Razor Wyświetl** i nazwij nowy widok *UpdateCourseCredits. cshtml*.

W obszarze *widoki/kursy/UpdateCourseCredits. cshtml* Zastąp kod szablonu następującym kodem:

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Uruchom `UpdateCourseCredits` metodę, wybierając kartę **kursy** , a następnie dodając wartość "/UpdateCourseCredits" na końcu adresu URL na pasku adresu przeglądarki (na przykład: `http://localhost:5813/Courses/UpdateCourseCredits` ). Wprowadź liczbę w polu tekstowym:

![Strona aktualizacji kredytów kursu](advanced/_static/update-credits.png)

Kliknij przycisk **Update** (Aktualizuj). Zostanie wyświetlona liczba zaatakowanych wierszy:

![Aktualizuj środki na stronie kredytów kursu](advanced/_static/update-credits-rows-affected.png)

Kliknij przycisk **Wstecz, aby** wyświetlić listę kursów o zmienionej liczbie kredytów.

Należy pamiętać, że kod produkcyjny zapewni, że aktualizacje będą zawsze powodować prawidłowe dane. Kod uproszczony przedstawiony w tym miejscu może pomnożyć liczbę kredytów wystarczającą do wyniku liczby większe niż 5. ( `Credits` Właściwość ma `[Range(0, 5)]` atrybut.) Kwerenda Update będzie działała, ale nieprawidłowe dane mogą spowodować nieoczekiwane wyniki w innych częściach systemu, które zakładają, że liczba kredytów wynosi 5 lub mniej.

Aby uzyskać więcej informacji na temat nieprzetworzonych zapytań SQL, zobacz [RAW zapytania SQL](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Badanie zapytań SQL

Czasami warto zobaczyć rzeczywiste zapytania SQL, które są wysyłane do bazy danych. Wbudowane funkcje rejestrowania dla ASP.NET Core są automatycznie używane przez EF Core do zapisywania dzienników zawierających dane SQL na potrzeby zapytań i aktualizacji. W tej sekcji zobaczysz przykłady rejestrowania w programie SQL Server.

Otwórz *StudentsController.cs* i w `Details` metodzie Ustaw punkt przerwania w `if (student == null)` instrukcji.

Uruchom aplikację w trybie debugowania i przejdź do strony szczegółów dla ucznia.

Przejdź do okna **dane wyjściowe** zawierające dane wyjściowe debugowania, a następnie Wyświetl zapytanie:

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

Zobaczysz coś tutaj, co może się zdarzyć: kod SQL wybiera do 2 wierszy ( `TOP(2)` ) z tabeli Person. `SingleOrDefaultAsync`Metoda nie ma wpływu na 1 wiersz na serwerze. Oto dlaczego:

* Jeśli zapytanie zwróci wiele wierszy, metoda zwraca wartość null.
* Aby określić, czy zapytanie zwróci wiele wierszy, EF musi sprawdzić, czy zwraca co najmniej 2.

Należy pamiętać, że nie jest konieczne używanie trybu debugowania i zatrzymywanie w punkcie przerwania w celu uzyskania danych wyjściowych rejestrowania w oknie **danych wyjściowych** . Jest to wygodny sposób na zatrzymanie rejestrowania w punkcie, w którym chcesz zobaczyć dane wyjściowe. Jeśli tego nie zrobisz, rejestrowanie jest kontynuowane, a użytkownik musi przewijać się ponownie, aby znaleźć interesujące Cię składniki.

## <a name="create-an-abstraction-layer"></a>Tworzenie warstwy abstrakcji

Wielu deweloperów pisze kod, aby zaimplementować wzorce repozytorium i jednostki pracy jako otokę otaczającą kod, który działa z Entity Framework. Wzorce te mają na celu utworzenie warstwy abstrakcji między warstwą dostępu do danych i warstwą logiki biznesowej aplikacji. Wdrożenie tych wzorców może pomóc w izolowaniu aplikacji od zmian w magazynie danych oraz w celu ułatwienia zautomatyzowanych testów jednostkowych lub projektowania opartego na testach (TDD). Jednak zapisanie dodatkowego kodu w celu zaimplementowania tych wzorców nie zawsze jest najlepszym wyborem dla aplikacji korzystających z EF z kilku powodów:

* Sama klasa kontekstu EF izolowana od kodu z kodu specyficznego dla magazynu danych.

* Klasa kontekstu EF może działać jako Klasa jednostki pracy dla aktualizacji bazy danych korzystających z EF.

* EF zawiera funkcje do implementowania usługi TDD bez pisania kodu repozytorium.

Informacje o sposobach implementacji wzorców repozytorium i jednostki pracy można znaleźć [w wersji Entity Framework 5 tej serii samouczków](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implementuje dostawcę bazy danych w pamięci, który może być używany do testowania. Aby uzyskać więcej informacji, zobacz [test with inMemory](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Automatyczne wykrywanie zmian

Entity Framework określa, w jaki sposób jednostka została zmieniona (i w związku z tym, które aktualizacje muszą być wysyłane do bazy danych), porównując bieżące wartości jednostki z oryginalnymi wartościami. Oryginalne wartości są przechowywane, gdy obiekt jest wysyłany lub dołączany. Niektóre metody, które powodują automatyczne wykrywanie zmian, są następujące:

* DbContext. metody SaveChanges

* DbContext. entry

* ChangeTracker. wpisy

Jeśli śledzisz dużą liczbę jednostek i wywołujesz jedną z tych metod wiele razy w pętli, możesz uzyskać znaczące ulepszenia wydajności, tymczasowo wyłączając automatyczne wykrywanie zmian przy użyciu `ChangeTracker.AutoDetectChangesEnabled` właściwości. Na przykład:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Core kod źródłowy i plany programistyczne

Źródło Entity Framework Core ma wartość [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) . Repozytorium EF Core zawiera nocne kompilacje, Śledzenie problemów, specyfikacje funkcji, projektowanie notatek na spotkaniu i [plan do przyszłego rozwoju](https://github.com/dotnet/efcore/wiki/Roadmap). Można tworzyć i znajdować usterki oraz współtworzyć.

Chociaż kod źródłowy jest otwarty, Entity Framework Core jest w pełni obsługiwany jako produkt firmy Microsoft. Zespół Entity Framework firmy Microsoft zachowuje kontrolę nad tym, jakie wkłady są akceptowane i sprawdza wszystkie zmiany kodu w celu zapewnienia jakości każdej wersji.

## <a name="reverse-engineer-from-existing-database"></a>Odtwarzanie z istniejącej bazy danych

Aby odtworzyć model danych, w tym klasy jednostek z istniejącej bazy danych, użyj polecenia [szkielet-DbContext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) . Zobacz [samouczek z wprowadzeniem](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Używanie dynamicznego LINQ do uproszczenia kodu

[Trzeci samouczek z tej serii](sort-filter-page.md) pokazuje, jak pisać kod LINQ przez znakowanie nazw kolumn w `switch` instrukcji. Z dwiema kolumnami do wyboru, to działa prawidłowo, ale jeśli masz wiele kolumn, kod może uzyskać pełne informacje. Aby rozwiązać ten problem, można użyć metody, `EF.Property` Aby określić nazwę właściwości jako ciąg. Aby wypróbować to podejście, Zastąp `Index` metodę w `StudentsController` poniższym kodzie.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Podziękowania

Tomasz Dykstra i Rick Anderson (Twitter @RickAndMSFT ) zapisały ten samouczek. Rowan Miller, Diego Vega i inni członkowie zespołu Entity Framework mogą uzyskać przeglądy kodu i pomóc w debugowaniu problemów, które powstały podczas pisania kodu dla samouczków. Jan Goldman i Paul pracował nad aktualizacją samouczka dla ASP.NET Core 2,2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Rozwiązywanie typowych problemów

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll używany przez inny proces

Komunikat o błędzie:

> Nie można otworzyć elementu "...bin\Debug\netcoreapp1.0\ContosoUniversity.dll" do zapisu — "proces nie może uzyskać dostępu do pliku" ...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll ", ponieważ jest on używany przez inny proces.

Rozwiązanie:

Zatrzymaj lokację w IIS Express. Przejdź do paska zadań systemu Windows, Znajdź IIS Express i kliknij prawym przyciskiem myszy jego ikonę, wybierz witrynę firmy Contoso University, a następnie kliknij pozycję **Zatrzymaj lokację**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Migracja szkieletowa bez kodu w metodach up i Down

Możliwa przyczyna:

Polecenia EF CLI nie są automatycznie zamykane i zapisują pliki kodu. Jeśli masz niezapisane zmiany po uruchomieniu `migrations add` polecenia, EF nie znajdzie zmian.

Rozwiązanie:

Uruchom `migrations remove` polecenie, Zapisz zmiany kodu i ponownie uruchom `migrations add` polecenie.

### <a name="errors-while-running-database-update"></a>Błędy podczas uruchamiania aktualizacji bazy danych

Podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane, można uzyskać inne błędy. Jeśli wystąpią błędy migracji, nie można rozwiązać tego problemu, możesz zmienić nazwę bazy danych w parametrach połączenia lub usunąć bazę danych. W przypadku nowej bazy danych nie ma żadnych danych do migracji, a polecenie Update-Database jest znacznie bardziej gotowe do wykonania bez błędów.

Najprostszym podejściem jest zmiana nazwy bazy danych w programie *appsettings.json* . Przy następnym uruchomieniu `database update` zostanie utworzona nowa baza danych.

Aby usunąć bazę danych w programie SSOX, kliknij prawym przyciskiem myszy bazę danych, kliknij polecenie **Usuń**, a następnie w oknie dialogowym **Usuwanie bazy danych** wybierz pozycję **Zamknij istniejące połączenia** i kliknij przycisk **OK**.

Aby usunąć bazę danych przy użyciu interfejsu wiersza polecenia, uruchom `database drop` polecenie CLI:

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Błąd lokalizowania wystąpienia SQL Server

Komunikat o błędzie:

> Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (Dostawca: interfejsy sieciowe SQL, błąd: 26 — błąd lokalizowania określonego serwera/wystąpienia)

Rozwiązanie:

Sprawdź parametry połączenia. Jeśli plik bazy danych został ręcznie usunięty, Zmień nazwę bazy danych w ciągu konstrukcyjnym, aby zacząć od nowa baza danych.

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat EF Core, zobacz [dokumentację Entity Framework Core](/ef/core). Dostępna jest również książka: [Entity Framework Core w działaniu](https://www.manning.com/books/entity-framework-core-in-action).

Aby uzyskać informacje na temat sposobu wdrażania aplikacji sieci Web, zobacz <xref:host-and-deploy/index> .

Aby uzyskać informacje dotyczące innych tematów odnoszących się do ASP.NET Core MVC, takich jak uwierzytelnianie i autoryzacja, zobacz <xref:index> .

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Wykonywanie nieprzetworzonych zapytań SQL
> * Wywołuje zapytanie w celu zwrócenia jednostek
> * Wywoływana kwerenda zwracająca inne typy
> * Nazywane kwerendą Update
> * Zbadaj zapytania SQL
> * Utworzono warstwę abstrakcji
> * Informacje o automatycznym wykrywaniu zmian
> * Informacje o EF Core kodzie źródłowym i planach programistycznych
> * Dowiesz się, jak używać dynamicznego LINQ do uproszczenia kodu

Ta seria samouczków została zakończona przy użyciu Entity Framework Core w aplikacji ASP.NET Core MVC. Ta seria pracowała z nową bazą danych; Alternatywą jest odtwarzanie modelu z istniejącej bazy danych.

> [!div class="nextstepaction"]
> [Samouczek: EF Core z MVC, istniejąca baza danych](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
