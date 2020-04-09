---
title: Strony brzytwy z EF Core w rdzeniu ASP.NET - CRUD - 2 z 8
author: rick-anderson
description: Pokazuje, jak tworzyć, czytać, aktualizować, usuwać za pomocą EF Core.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/crud
ms.openlocfilehash: 05519852fab22bd3ad5b77e3494b49191448286f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665649"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a>Strony brzytwy z EF Core w rdzeniu ASP.NET - CRUD - 2 z 8

Przez [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)i Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku szkieletu CRUD (tworzenie, odczyt, aktualizacja, usuwanie) kod jest sprawdzany i dostosowywany.

## <a name="no-repository"></a>Brak repozytorium

Niektórzy deweloperzy używają warstwy usługi lub wzorca repozytorium do tworzenia warstwy abstrakcji między interfejsem użytkownika (Strony Razor) a warstwą dostępu do danych. Ten poradnik tego nie robi. Aby zminimalizować złożoność i zachować samouczek koncentruje się na EF Core, kod EF Core jest dodawany bezpośrednio do klas modelu strony. 

## <a name="update-the-details-page"></a>Aktualizowanie strony Szczegóły

Szkieletowy kod dla stron Studenci nie zawiera danych rejestracji. W tej sekcji należy dodać rejestracje do strony Szczegóły.

### <a name="read-enrollments"></a>Odczytywanie rejestracji

Aby wyświetlić dane rejestracji ucznia na stronie, musisz je przeczytać. Szkieletowy kod w *pages/students/details.cshtml.cs* odczytuje tylko dane ucznia bez danych rejestracji:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Zastąp `OnGetAsync` metodę następującym kodem, aby odczytać dane rejestracji dla wybranego ucznia. Zmiany są wyróżnione.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

[Metody Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) i [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) powodują, `Student.Enrollments` że kontekst ma załadować `Enrollment.Course` właściwość nawigacji, a w ramach każdej rejestracji właściwość nawigacji. Te metody są szczegółowo badane w [samouczku odczytu danych powiązanych.](xref:data/ef-rp/read-related-data)

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) Metoda zwiększa wydajność w scenariuszach, w których zwracane jednostki nie są aktualizowane w bieżącym kontekście. `AsNoTracking`jest omówiony w dalszej części tego samouczka.

### <a name="display-enrollments"></a>Wyświetlanie rejestracji

Zastąp kod w *pages/students/details.cshtml* następującym kodem, aby wyświetlić listę rejestracji. Zmiany są wyróżnione.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Poprzedni kod pętli za pośrednictwem jednostek `Enrollments` we właściwości nawigacji. Dla każdej rejestracji wyświetla tytuł kursu i klasę. Tytuł kursu jest pobierany z Course jednostki, `Course` która jest przechowywana we właściwości nawigacji enrollments jednostki.

Uruchom aplikację, wybierz kartę **Uczniowie** i kliknij **łącze Szczegóły** dla ucznia. Zostanie wyświetlona lista kursów i ocen dla wybranego ucznia.

### <a name="ways-to-read-one-entity"></a>Sposoby odczytywania jednej encji

Wygenerowany kod używa [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) do odczytu jednej jednostki. Ta metoda zwraca wartość null, jeśli nic nie zostanie znalezione; w przeciwnym razie zwraca pierwszy wiersz znaleziony, który spełnia kryteria filtru kwerendy. `FirstOrDefaultAsync`jest ogólnie lepszym wyborem niż następujące alternatywy:

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) — zgłasza wyjątek, jeśli istnieje więcej niż jedna jednostka, która spełnia filtr zapytań. Aby ustalić, czy kwerenda może zwrócić `SingleOrDefaultAsync` więcej niż jeden wiersz, spróbuj pobrać wiele wierszy. Ta dodatkowa praca jest niepotrzebna, jeśli kwerenda może zwrócić tylko jedną jednostkę, tak jak podczas wyszukiwania przy unikatowym kluczu.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) — wyszukuje jednostkę z kluczem podstawowym (PK). Jeśli jednostka z PK jest śledzona przez kontekst, jest zwracany bez żądania do bazy danych. Ta metoda jest zoptymalizowana pod kątem wyszukiwania pojedynczej `Include` jednostki, ale nie można wywołać z . `FindAsync`  Więc jeśli potrzebne są `FirstOrDefaultAsync` powiązane dane, jest lepszym wyborem.

### <a name="route-data-vs-query-string"></a>Rozsyłanie danych a ciągiem zapytania

Adres URL strony Szczegóły `https://localhost:<port>/Students/Details?id=1`to . Wartość klucza podstawowego jednostki znajduje się w ciągu zapytania. Niektórzy deweloperzy wolą przekazać wartość klucza w danych trasy: `https://localhost:<port>/Students/Details/1`. Aby uzyskać więcej informacji, zobacz [Aktualizowanie wygenerowanego kodu](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Aktualizowanie strony Tworzenie

`OnPostAsync` Szkieletowy kod strony Tworzenie jest narażony na [nadpostanie](#overposting). Zastąp `OnPostAsync` metodę w *pages/students/create.cshtml.cs* następującym kodem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Poprzedni kod tworzy obiekt Student, a następnie używa zaksięgowanych pól formularza do aktualizacji właściwości obiektu Student. [Metoda TryUpdateModelAsync:](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_)

* Używa zaksięgowanych wartości formularza z właściwości [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) we wzorze [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Aktualizuje tylko wymienione`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`właściwości ( ).
* Wyszukuje pola formularza z prefiksem "student". Na przykład `Student.FirstMidName`. Nie jest rozróżniana wielkość liter.
* Używa systemu [wiązania modelu](xref:mvc/models/model-binding) do konwersji wartości formularza z `Student` ciągów do typów w modelu. Na przykład `EnrollmentDate` musi zostać przekonwertowany na DateTime.

Uruchom aplikację i utwórz encję ucznia, aby przetestować stronę Utwórz.

## <a name="overposting"></a>Nadpostowanie

Używanie `TryUpdateModel` do aktualizowania pól z zaksięgowanych wartości jest najlepszym rozwiązaniem w zakresie zabezpieczeń, ponieważ zapobiega nadpostowywaniu. Załóżmy na przykład, `Secret` że jednostka Student zawiera właściwość, której ta strona sieci web nie powinna aktualizować ani dodawać:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Nawet jeśli aplikacja nie ma `Secret` pola na stronie tworzenia lub aktualizacji Razor, `Secret` haker może ustawić wartość przez nadpostanie. Haker może użyć narzędzia, takiego jak Fiddler, lub napisać `Secret` javascript, aby opublikować wartość formularza. Oryginalny kod nie ogranicza pola, które używa spinacza modelu podczas tworzenia wystąpienia studenta.

Niezależnie od wartości, jaką `Secret` haker określił dla pola formularza, jest aktualizowana w bazie danych. Na poniższej ilustracji przedstawiono `Secret` narzędzie Fiddler dodające pole (z wartością "OverPost") do zaksięgowanych wartości formularza.

![Skrzypek dodający pole Tajne](../ef-mvc/crud/_static/fiddler.png)

Wartość "OverPost" jest pomyślnie `Secret` dodana do właściwości wstawionego wiersza. Dzieje się tak, mimo że `Secret` projektant aplikacji nigdy nie zamierzał, aby właściwość została ustawiona za pomocą strony Utwórz.

### <a name="view-model"></a>Wyświetl model

Wyświetlanie modeli zapewniają alternatywny sposób, aby zapobiec overposting.

Model aplikacji jest często nazywany modelem domeny. Model domeny zazwyczaj zawiera wszystkie właściwości wymagane przez odpowiednią jednostkę w bazie danych. Model widoku zawiera tylko właściwości potrzebne dla interfejsu użytkownika, który jest używany (na przykład Create strony).

Oprócz modelu widoku niektóre aplikacje używają modelu wiązania lub modelu wejściowego do przekazywania danych między klasą modelu strony Razor Pages a przeglądarką. 

Należy wziąć `Student` pod uwagę następujący model widoku:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

Poniższy kod używa `StudentVM` modelu widoku do utworzenia nowego ucznia:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) Metoda ustawia wartości tego obiektu przez odczyt wartości z innego [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) obiektu. `SetValues`używa dopasowywania nazw właściwości. Typ modelu widoku nie musi być powiązany z typem modelu, po prostu musi mieć właściwości, które pasują.

Korzystanie `StudentVM` z wymaga [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) być aktualizowane do użycia, `StudentVM` a nie `Student`.

## <a name="update-the-edit-page"></a>Aktualizowanie strony edycji

W *pages/students/edit.cshtml.cs*, `OnGetAsync` `OnPostAsync` zastąp i metody następującym kodem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Zmiany kodu są podobne do strony Utwórz z kilkoma wyjątkami:

* `FirstOrDefaultAsync`został zastąpiony przez [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Jeśli nie musisz dołączać powiązanych `FindAsync` danych, jest bardziej wydajny.
* `OnPostAsync`ma `id` parametr.
* Bieżący uczeń jest pobierany z bazy danych, a nie tworzenie pustego ucznia.

Uruchom aplikację i przetestuj ją, tworząc i edytując ucznia.

## <a name="entity-states"></a>Państwa członkowskie encji

Kontekst bazy danych śledzi, czy jednostki w pamięci są zsynchronizowane z odpowiadającymi im wierszami w bazie danych. Te informacje śledzenia określa, co się dzieje, gdy [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) jest wywoływana. Na przykład, gdy nowa encja jest przekazywana do [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) metody, stan tej jednostki jest ustawiona na [Dodano](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Po `SaveChangesAsync` wywołaniu kontekst bazy danych wystawia polecenie SQL INSERT.

Jednostka może znajdować się w jednym z [następujących stanów:](/dotnet/api/microsoft.entityframeworkcore.entitystate)

* `Added`: Jednostka jeszcze nie istnieje w bazie danych. Metoda `SaveChanges` wystawia instrukcję INSERT.

* `Unchanged`: Nie trzeba zapisywać żadnych zmian w tej encji. Jednostka ma ten stan, gdy jest odczytywany z bazy danych.

* `Modified`: Niektóre lub wszystkie wartości właściwości jednostki zostały zmodyfikowane. Metoda `SaveChanges` wystawia instrukcję UPDATE.

* `Deleted`: Jednostka została oznaczona do usunięcia. Metoda `SaveChanges` wystawia instrukcję DELETE.

* `Detached`: Jednostka nie jest śledzona przez kontekst bazy danych.

W aplikacji klasycznej zmiany stanu są zazwyczaj ustawiane automatycznie. Jednostka jest odczytywana, wprowadzane są zmiany, a `Modified`stan jednostki jest automatycznie zmieniany na . Wywołanie `SaveChanges` generuje instrukcję SQL UPDATE, która aktualizuje tylko zmienione właściwości.

W aplikacji sieci `DbContext` web, która odczytuje jednostkę i wyświetla dane są usuwane po renderowaniu strony. Gdy `OnPostAsync` wywoływana jest metoda strony, jest składane nowe żądanie sieci `DbContext`web i z nowym wystąpieniem . Ponowne odczytanie jednostki w tym nowym kontekście symuluje przetwarzanie pulpitu.

## <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

W tej sekcji zaimplementujesz niestandardowy `SaveChanges` komunikat o błędzie, gdy wywołanie kończy się niepowodzeniem.

Zastąp kod w *pages/students/delete.cshtml.cs* następującym kodem. Zmiany są wyróżnione (inne niż `using` oczyszczanie instrukcji).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

Poprzedni kod dodaje parametr `saveChangesError` opcjonalny `OnGetAsync` do podpisu metody. `saveChangesError`wskazuje, czy metoda została wywołana po nieutwoniu obiektu ucznia. Operacja usuwania może zakończyć się niepowodzeniem z powodu przejściowych problemów z siecią. Błędy sieci przejściowej są bardziej prawdopodobne, gdy baza danych znajduje się w chmurze. Parametr `saveChangesError` jest false, gdy `OnGetAsync` strona Delete jest wywoływana z interfejsu użytkownika. Gdy `OnGetAsync` jest `OnPostAsync` wywoływana przez (ponieważ operacja `saveChangesError` usuwania nie powiodła się), parametr jest true.

Metoda `OnPostAsync` pobiera zaznaczoną encję, a następnie wywołuje metodę [Usuń,](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) aby ustawić stan encji na `Deleted`. Po `SaveChanges` wywołaniu jest generowane polecenie SQL DELETE. Jeśli `Remove` się nie powiedzie:

* Wyjątek bazy danych jest przechwytyny.
* Metoda Usuń `OnGetAsync` strony jest `saveChangesError=true`wywoływana z plikiem .

Dodaj komunikat o błędzie do strony Usuń maszynkę *(Pages/Students/Delete.cshtml):*

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Uruchom aplikację i usuń ucznia, aby przetestować stronę Usuń.

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/intro)
> [Następny samouczek](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku szkieletu CRUD (tworzenie, odczyt, aktualizacja, usuwanie) kod jest sprawdzany i dostosowywany.

Aby zminimalizować złożoność i zachować te samouczki koncentruje się na EF Core, EF Core kod jest używany w modelach stron. Niektórzy deweloperzy używają warstwy usługi lub wzorca repozytorium, aby utworzyć warstwę abstrakcji między interfejsem użytkownika (Strony Razor) a warstwą dostępu do danych.

W tym samouczku zostaną zbadane strony Utwórz, Edytuj, Usuń i Szczegóły Razor w folderze *Studenci.*

Szkieletowy kod używa następującego wzorca dla stron Tworzenie, Edytowanie i Usuwanie:

* Pobierz i wyświetl żądane dane za `OnGetAsync`pomocą metody HTTP GET .
* Zapisz zmiany w danych za `OnPostAsync`pomocą metody HTTP POST .

Strony Indeks i szczegóły otrzymują i wyświetlają żądane dane za pomocą metody HTTP GET`OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync vs. FirstOrDefaultAsync

Wygenerowany kod używa [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), który jest ogólnie preferowany niż [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

 `FirstOrDefaultAsync`jest bardziej `SingleOrDefaultAsync` wydajny niż pobieranie jednego podmiotu:

* Chyba że kod musi sprawdzić, czy nie ma więcej niż jedna jednostka zwrócona z kwerendy.
* `SingleOrDefaultAsync`pobiera więcej danych i wykonuje niepotrzebną pracę.
* `SingleOrDefaultAsync`zgłasza wyjątek, jeśli istnieje więcej niż jedna jednostka, która pasuje do części filtru.
* `FirstOrDefaultAsync`nie zgłasza, jeśli istnieje więcej niż jedna jednostka, która pasuje do części filtru.

<a name="FindAsync"></a>

### <a name="findasync"></a>Znajdźasync

W dużej części kodu rusztowania, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) może `FirstOrDefaultAsync`być używany zamiast .

`FindAsync`:

* Znajduje jednostkę z kluczem podstawowym (PK). Jeśli jednostka z PK jest śledzona przez kontekst, jest zwracany bez żądania do bazy danych.
* Jest prosty i zwięzły.
* Jest zoptymalizowany do wyszukiwania pojedynczej jednostki.
* Może mieć korzyści z korzyści w niektórych sytuacjach, ale rzadko zdarza się to w przypadku typowych aplikacji sieci web.
* Niejawnie używa [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) zamiast [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

Ale jeśli chcesz `Include` do innych `FindAsync` jednostek, to nie jest już odpowiednie. Oznacza to, że może `FindAsync` być konieczne porzucenie i przejście do kwerendy w miarę postępów aplikacji.

## <a name="customize-the-details-page"></a>Dostosowywanie strony Szczegóły

Przejdź do `Pages/Students` strony. Łącza **Edytuj,** **Szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Students/Index.cshtml.*

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Uruchom aplikację i wybierz łącze **Szczegóły.** Adres URL jest `http://localhost:5000/Students/Details?id=2`formularzem . Identyfikator ucznia jest przekazywany`?id=2`przy użyciu ciągu zapytania ( ).

Zaktualizuj strony Edytuj, Szczegóły i `"{id:int}"` Usuń razor, aby użyć szablonu trasy. Zmień dyrektywę strony dla każdej `@page` `@page "{id:int}"`z tych stron z .

Żądanie na stronie z szablonem trasy "{id:int}", które **nie** zawiera wartości trasy całkowitej, zwraca błąd HTTP 404 (nie znaleziono). Na przykład `http://localhost:5000/Students/Details` zwraca błąd 404. Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Uruchom aplikację, kliknij link Szczegóły i sprawdź, czy adres URL przekazuje`http://localhost:5000/Students/Details/2`identyfikator jako dane trasy ( ).

Nie zmieniaj `@page` się `@page "{id:int}"`globalnie na , w ten sposób przerywa łącza do stron Główna i Utwórz.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Dodawanie powiązanych danych

Szkieletowy kod dla studenta indeks strony nie `Enrollments` zawiera właściwości. W tej sekcji zawartość `Enrollments` kolekcji jest wyświetlana na stronie Szczegóły.

Metoda `OnGetAsync` *Pages/Students/Details.cshtml.cs* używa `FirstOrDefaultAsync` tej metody do pobierania `Student` pojedynczej jednostki. Dodaj następujący wyróżniony kod:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

[Metody Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) i [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) powodują, `Student.Enrollments` że kontekst ma załadować `Enrollment.Course` właściwość nawigacji, a w ramach każdej rejestracji właściwość nawigacji. Metody te są szczegółowo badane w samouczku danych związanych z odczytem.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) Metoda zwiększa wydajność w scenariuszach, gdy zwracane jednostki nie są aktualizowane w bieżącym kontekście. `AsNoTracking`jest omówiony w dalszej części tego samouczka.

### <a name="display-related-enrollments-on-the-details-page"></a>Wyświetlanie powiązanych rejestracji na stronie Szczegóły

Otwórz *strony/Studenci/Szczegóły.cshtml*. Dodaj następujący wyróżniony kod, aby wyświetlić listę rejestracji:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Jeśli wcięcie kodu jest nieprawidłowe po wklejeniu kodu, naciśnij klawisz CTRL-K-D, aby go poprawić.

Poprzedni kod pętli za pośrednictwem jednostek `Enrollments` we właściwości nawigacji. Dla każdej rejestracji wyświetla tytuł kursu i klasę. Tytuł kursu jest pobierany z Course jednostki, `Course` która jest przechowywana we właściwości nawigacji enrollments jednostki.

Uruchom aplikację, wybierz kartę **Uczniowie** i kliknij **łącze Szczegóły** dla ucznia. Zostanie wyświetlona lista kursów i ocen dla wybranego ucznia.

## <a name="update-the-create-page"></a>Aktualizowanie strony Tworzenie

Zaktualizuj `OnPostAsync` metodę w *pages/students/create.cshtml.cs* za pomocą następującego kodu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Sprawdź [kod TryUpdateModelAsync:](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_)

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

W poprzednim kodzie `TryUpdateModelAsync<Student>` próbuje zaktualizować `emptyStudent` obiekt przy użyciu zaksięgowanych wartości formularza z [pagecontext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) właściwości w [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel). `TryUpdateModelAsync`aktualizuje tylko wymienione`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`właściwości ( ).

W poprzedniej próbce:

* Drugi argument`"student", // Prefix`( ) jest prefiks używa do wyszukiwania wartości. Nie jest rozróżniana wielkość liter.
* Zaksięgowane wartości formularza są konwertowane na typy w `Student` modelu przy użyciu powiązania [modelu](xref:mvc/models/model-binding).

<a id="overpost"></a>

### <a name="overposting"></a>Nadpostowanie

Używanie `TryUpdateModel` do aktualizowania pól z zaksięgowanych wartości jest najlepszym rozwiązaniem w zakresie zabezpieczeń, ponieważ zapobiega nadpostowywaniu. Załóżmy na przykład, `Secret` że jednostka Student zawiera właściwość, której ta strona sieci web nie powinna aktualizować ani dodawać:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Nawet jeśli aplikacja nie ma `Secret` pola na stronie razor tworzenia / aktualizacji, `Secret` haker może ustawić wartość przez overposting. Haker może użyć narzędzia, takiego jak Fiddler, lub napisać `Secret` javascript, aby opublikować wartość formularza. Oryginalny kod nie ogranicza pola, które używa spinacza modelu podczas tworzenia wystąpienia studenta.

Niezależnie od wartości, jaką `Secret` haker określił dla pola formularza, jest aktualizowana w bazy danych. Na poniższej ilustracji przedstawiono `Secret` narzędzie Fiddler dodające pole (z wartością "OverPost") do zaksięgowanych wartości formularza.

![Skrzypek dodający pole Tajne](../ef-mvc/crud/_static/fiddler.png)

Wartość "OverPost" jest pomyślnie `Secret` dodana do właściwości wstawionego wiersza. Projektant aplikacji nigdy `Secret` nie zamierzał, aby właściwość została ustawiona za pomocą strony Utwórz.

<a name="vm"></a>

### <a name="view-model"></a>Wyświetl model

Model widoku zazwyczaj zawiera podzbiór właściwości zawartych w modelu używanym przez aplikację. Model aplikacji jest często nazywany modelem domeny. Model domeny zazwyczaj zawiera wszystkie właściwości wymagane przez odpowiednią jednostkę w db. Model widoku zawiera tylko właściwości potrzebne dla warstwy interfejsu użytkownika (na przykład strona Utwórz). Oprócz modelu widoku niektóre aplikacje używają modelu wiązania lub modelu wejściowego do przekazywania danych między klasą modelu strony Razor Pages a przeglądarką. Należy wziąć `Student` pod uwagę następujący model widoku:

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Wyświetlanie modeli zapewniają alternatywny sposób, aby zapobiec overposting. Model widoku zawiera tylko właściwości do wyświetlenia (wyświetlenia) lub aktualizacji.

Poniższy kod używa `StudentVM` modelu widoku do utworzenia nowego ucznia:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) Metoda ustawia wartości tego obiektu przez odczyt wartości z innego [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) obiektu. `SetValues`używa dopasowywania nazw właściwości. Typ modelu widoku nie musi być powiązany z typem modelu, po prostu musi mieć właściwości, które pasują.

Korzystanie `StudentVM` wymaga [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) być `StudentVM` aktualizowane do użycia, a nie `Student`.

W razor Pages `PageModel` klasy pochodnej jest model widoku.

## <a name="update-the-edit-page"></a>Aktualizowanie strony edycji

Zaktualizuj model strony dla strony Edytuj. Główne zmiany są wyróżnione:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Zmiany kodu są podobne do strony Utwórz z kilkoma wyjątkami:

* `OnPostAsync`ma parametr `id` opcjonalny.
* Bieżący uczeń jest pobierany z bazy danych, zamiast tworzyć pustego ucznia.
* `FirstOrDefaultAsync`został zastąpiony przez [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). `FindAsync`jest dobrym wyborem przy wyborze jednostki z klucza podstawowego. Aby uzyskać więcej informacji, zobacz [FindAsync.](#FindAsync)

### <a name="test-the-edit-and-create-pages"></a>Testowanie stron Edytowanie i tworzenie

Utwórz i edytuj kilka jednostek uczniów.

## <a name="entity-states"></a>Państwa członkowskie encji

Kontekst bazy danych śledzi, czy jednostki w pamięci są zsynchronizowane z odpowiadającymi im wierszami w bazy danych. Informacje o synchronizacji kontekstu bazy danych określa, co się dzieje, gdy [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) jest wywoływana. Na przykład, gdy nowa encja jest przekazywana do [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) metody, stan tej jednostki jest ustawiona na [Dodano](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Po `SaveChangesAsync` wywołaniu kontekst bazy danych wystawia polecenie SQL INSERT.

Jednostka może znajdować się w jednym z [następujących stanów:](/dotnet/api/microsoft.entityframeworkcore.entitystate)

* `Added`: Jednostka jeszcze nie istnieje w bazy danych. Metoda `SaveChanges` wystawia instrukcję INSERT.

* `Unchanged`: Nie trzeba zapisywać żadnych zmian w tej encji. Jednostka ma ten stan, gdy jest odczytywany z bazy danych.

* `Modified`: Niektóre lub wszystkie wartości właściwości jednostki zostały zmodyfikowane. Metoda `SaveChanges` wystawia instrukcję UPDATE.

* `Deleted`: Jednostka została oznaczona do usunięcia. Metoda `SaveChanges` wystawia instrukcję DELETE.

* `Detached`: Jednostka nie jest śledzona przez kontekst bazy danych.

W aplikacji klasycznej zmiany stanu są zazwyczaj ustawiane automatycznie. Jednostka jest odczytywana, wprowadzane są zmiany, a stan `Modified`encji, który ma zostać automatycznie zmieniony na . Wywołanie `SaveChanges` generuje instrukcję SQL UPDATE, która aktualizuje tylko zmienione właściwości.

W aplikacji sieci `DbContext` web, która odczytuje jednostkę i wyświetla dane są usuwane po renderowaniu strony. Gdy `OnPostAsync` wywoływana jest metoda strony, jest składane nowe żądanie sieci `DbContext`web i z nowym wystąpieniem . Ponowne odczytanie jednostki w tym nowym kontekście symuluje przetwarzanie pulpitu.

## <a name="update-the-delete-page"></a>Aktualizowanie strony Usuwanie

W tej sekcji kod jest dodawany do implementacji `SaveChanges` niestandardowego komunikatu o błędzie, gdy wywołanie nie powiedzie się. Dodaj ciąg zawierający możliwe komunikaty o błędach:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Zastąp metodę `OnGetAsync` poniższym kodem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Powyższy kod zawiera parametr `saveChangesError`opcjonalny . `saveChangesError`wskazuje, czy metoda została wywołana po nieutwoniu obiektu ucznia. Operacja usuwania może zakończyć się niepowodzeniem z powodu przejściowych problemów z siecią. Błędy sieci przejściowej są bardziej prawdopodobne w chmurze. `saveChangesError`false, gdy strona `OnGetAsync` Usuń jest wywoływana z interfejsu użytkownika. Gdy `OnGetAsync` jest `OnPostAsync` wywoływana przez (ponieważ operacja `saveChangesError` usuwania nie powiodła się), parametr jest true.

### <a name="the-delete-pages-onpostasync-method"></a>Metoda Usuń strony OnPostAsync

Wymień poniższy `OnPostAsync` kod:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Powyższy kod pobiera wybrany encję, a następnie wywołuje metodę [Usuń,](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) aby ustawić stan encji na `Deleted`. Po `SaveChanges` wywołaniu jest generowane polecenie SQL DELETE. Jeśli `Remove` się nie powiedzie:

* Wyjątek bazy danych zostanie przechwycony.
* Metoda Usuń `OnGetAsync` strony jest `saveChangesError=true`wywoływana z plikiem .

### <a name="update-the-delete-razor-page"></a>Aktualizowanie strony Usuń Maszynkę Do golenia

Dodaj podświetlony komunikat o błędzie do strony Usuń maszynkę do golenia.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Usuń test.

## <a name="common-errors"></a>Typowe błędy

Studenci / Indeks lub inne linki nie działają:

Sprawdź, czy strona Razor zawiera poprawną `@page` dyrektywę. Na przykład strona Students/Index Razor **nie** powinna zawierać szablonu trasy:

```cshtml
@page "{id:int}"
```

Każda strona Razor `@page` musi zawierać dyrektywę.



## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/intro)
> [następny](xref:data/ef-rp/sort-filter-page)

::: moniker-end
