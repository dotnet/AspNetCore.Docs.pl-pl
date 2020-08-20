---
title: Część 2, Razor strony z EF Core w ASP.NET Core — CRUD
author: rick-anderson
description: Część 2 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.date: 07/22/2019
no-loc:
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
uid: data/ef-rp/crud
ms.openlocfilehash: a6a99d736a60a55b81eb7e852413dc52b733d2fb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627536"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a>Część 2, Razor strony z EF Core w ASP.NET Core — CRUD

Autorzy [Dykstra](https://github.com/tdykstra), [Jan P Kowalski](https://twitter.com/thereformedprog)i [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku kod szkieletu CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie) zostanie sprawdzony i dostosowany.

## <a name="no-repository"></a>Brak repozytorium

Niektórzy Deweloperzy używają warstwy usług lub wzorca repozytorium, aby utworzyć warstwę abstrakcji między interfejsem użytkownika ( Razor stronami) i warstwą dostępu do danych. Ten samouczek nie działa. Aby zminimalizować złożoność i utrzymywać samouczek na EF Core, kod EF Core zostanie dodany bezpośrednio do klas modelu strony. 

## <a name="update-the-details-page"></a>Aktualizowanie strony szczegółów

Kod szkieletowy dla stron uczniów nie obejmuje danych rejestracji. W tej sekcji dowiesz się, jak dodać rejestracje do strony szczegółów.

### <a name="read-enrollments"></a>Odczytaj rejestracje

Aby wyświetlić na stronie dane rejestracyjne ucznia, należy je przeczytać. Kod szkieletowy na *stronach/studentów/szczegóły. cshtml. cs* odczytuje tylko dane uczniów, bez danych rejestracji:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Zastąp `OnGetAsync` metodę poniższym kodem, aby odczytywać dane rejestracyjne dla wybranego ucznia. Zmiany są wyróżnione.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

Metody [include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) i [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) powodują, że kontekst ładuje `Student.Enrollments` Właściwość nawigacji i w ramach każdej rejestracji `Enrollment.Course` właściwości nawigacji. Te metody są szczegółowo opisane w samouczku [odczytywanie powiązanych danych](xref:data/ef-rp/read-related-data) .

Metoda [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) zwiększa wydajność w scenariuszach, w których zwrócone jednostki nie są aktualizowane w bieżącym kontekście. `AsNoTracking` omówiono w dalszej części tego samouczka.

### <a name="display-enrollments"></a>Wyświetl rejestracje

Zastąp kod w obszarze *Pages/Students/details. cshtml* następującym kodem, aby wyświetlić listę rejestracji. Zmiany są wyróżnione.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Poprzedni kod pętle za pomocą jednostek we `Enrollments` właściwości nawigacji. Dla każdej rejestracji jest wyświetlany tytuł kursu i Klasa. Tytuł kursu jest pobierany z jednostki kursu przechowywanej we `Course` właściwości nawigacji jednostki rejestracji.

Uruchom aplikację, wybierz kartę **studenci** i kliknij link **szczegóły** dla ucznia. Zostanie wyświetlona lista kursów i ocen dla wybranego ucznia.

### <a name="ways-to-read-one-entity"></a>Sposoby odczytywania jednej jednostki

Wygenerowany kod używa [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) do odczytu jednej jednostki. Ta metoda zwraca wartość null, jeśli nic nie zostanie znalezione; w przeciwnym razie zwraca pierwszy znaleziony wiersz, który spełnia kryteria filtru zapytania. `FirstOrDefaultAsync` jest ogólnie lepszym rozwiązaniem niż następujące alternatywy:

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) — zgłasza wyjątek, jeśli istnieje więcej niż jedna jednostka, która spełnia kryteria filtru zapytań. Aby określić, czy zapytanie może zwrócić więcej niż jeden wiersz, `SingleOrDefaultAsync` próbuje pobrać wiele wierszy. Ta dodatkowa operacja jest niezbędna, jeśli zapytanie może zwrócić tylko jedną jednostkę, tak jak podczas wyszukiwania unikatowego klucza.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) — umożliwia znalezienie jednostki z kluczem podstawowym (PK). Jeśli jednostka z PK jest śledzona przez kontekst, jest zwracana bez żądania do bazy danych. Ta metoda jest zoptymalizowana pod kątem wyszukiwania pojedynczej jednostki, ale nie można wywoływać `Include` z `FindAsync` .  Tak więc, jeśli dane pokrewne są zbędne, `FirstOrDefaultAsync` to lepszy wybór.

### <a name="route-data-vs-query-string"></a>Dane trasy a ciąg zapytania

Adres URL strony szczegółów to `https://localhost:<port>/Students/Details?id=1` . Wartość klucza podstawowego jednostki znajduje się w ciągu zapytania. Niektórzy deweloperzy wolą przekazać wartość klucza w polu dane trasy: `https://localhost:<port>/Students/Details/1` . Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie wygenerowanego kodu](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Aktualizowanie strony tworzenia

Kod szkieletowy `OnPostAsync` dla strony tworzenia jest narażony na [nadpublikowanie](#overposting). Zastąp `OnPostAsync` metodę w obszarze *Pages/Students/Create. cshtml. cs* poniższym kodem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Poprzedni kod tworzy obiekt studenta, a następnie używa opublikowanych pól formularza do aktualizowania właściwości obiektu ucznia. Metoda [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) :

* Używa opublikowanych wartości formularza z właściwości [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) w [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Aktualizuje tylko wymienione właściwości ( `s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate` ).
* Szuka pól formularza z prefiksem "Student". Na przykład `Student.FirstMidName`. Wielkość liter nie jest uwzględniana.
* Używa systemu [powiązań modelu](xref:mvc/models/model-binding) do konwersji wartości formularza z ciągów do typów w `Student` modelu. Na przykład, `EnrollmentDate` musi zostać przekonwertowany na typ DateTime.

Uruchom aplikację i Utwórz jednostkę ucznia, aby przetestować stronę tworzenie.

## <a name="overposting"></a>Przefinalizowanie

Używanie `TryUpdateModel` do aktualizowania pól z opublikowanymi wartościami jest najlepszym rozwiązaniem w zakresie zabezpieczeń, ponieważ uniemożliwia przekazanie. Załóżmy na przykład, że jednostka ucznia zawiera `Secret` Właściwość, której ta strona sieci Web nie powinna aktualizować ani dodawać:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Nawet jeśli aplikacja nie ma `Secret` pola na stronie Tworzenie lub aktualizowanie Razor , haker może ustawić wartość przez przepełnianie `Secret` . Haker może użyć narzędzia takiego jak programu Fiddler lub napisać kod JavaScript w celu opublikowania `Secret` wartości formularza. Oryginalny kod nie ogranicza pól używanych przez spinacz modelu podczas tworzenia wystąpienia ucznia.

Niezależnie od wartości, która hakera określona dla `Secret` pola formularza zostanie zaktualizowana w bazie danych. Na poniższej ilustracji przedstawiono Narzędzie programu Fiddler, które dodaje `Secret` pole (z wartością "naddawaj") do wartości zaksięgowanych formularzy.

![Programu Fiddler Dodawanie pola tajnego](../ef-mvc/crud/_static/fiddler.png)

Wartość "Zaksięguj" została pomyślnie dodana do `Secret` Właściwości wstawionego wiersza. Dzieje się tak, mimo że projektant aplikacji nigdy nie zamierzy `Secret` właściwości, które mają być ustawione przy użyciu strony Tworzenie.

### <a name="view-model"></a>Wyświetlanie modelu

Modele widoków zapewniają alternatywny sposób zapobiegania przepisywaniu.

Model aplikacji jest często nazywany modelem domeny. Model domeny zwykle zawiera wszystkie właściwości wymagane przez odpowiednią jednostkę w bazie danych. Model widoku zawiera tylko właściwości, które są niezbędne dla interfejsu użytkownika, który jest używany do (na przykład Strona tworzenia).

Poza modelem widoku niektóre aplikacje wykorzystują model powiązań lub model wejściowy do przekazywania danych między Razor klasą modelu strony stron i przeglądarką. 

Rozważmy następujący `Student` model widoku:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

Poniższy kod używa `StudentVM` modelu widoku do utworzenia nowego ucznia:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Metoda [setValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) ustawia wartości tego obiektu, odczytując wartości z innego obiektu [propertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) . `SetValues` używa dopasowywania nazw właściwości. Typ modelu widoku nie musi być powiązany z typem modelu, dlatego musi mieć już pasujące właściwości.

Użycie `StudentVM` wymaga, aby funkcja [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) była aktualizowana do użycia, `StudentVM` a nie `Student` .

## <a name="update-the-edit-page"></a>Aktualizowanie strony edytowania

W obszarze *strony/studenci/Edytuj. cshtml. cs*Zastąp `OnGetAsync` `OnPostAsync` metody i poniższym kodem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Zmiany w kodzie są podobne do strony tworzenie z kilkoma wyjątkami:

* `FirstOrDefaultAsync` został zastąpiony [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Gdy nie musisz zawierać powiązanych danych, `FindAsync` jest bardziej wydajne.
* `OnPostAsync` ma `id` parametr.
* Bieżący student jest pobierany z bazy danych, a nie od tworzenia pustego ucznia.

Uruchom aplikację i przetestuj ją, tworząc i edytując ucznia.

## <a name="entity-states"></a>Stany jednostek

Kontekst bazy danych śledzi, czy jednostki w pamięci są zsynchronizowane z odpowiadającymi im wierszami w bazie danych. Te informacje śledzenia decydują o tym, co się stanie w przypadku wywołania [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) . Na przykład gdy nowa jednostka jest przenoszona do metody [addasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) , stan tej jednostki jest ustawiany na wartość [dodana](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Gdy `SaveChangesAsync` jest wywoływana, kontekst bazy danych wystawia polecenie SQL INSERT.

Jednostka może być w jednym z [następujących stanów](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: Jednostka jeszcze nie istnieje w bazie danych. `SaveChanges`Metoda wystawia instrukcję INSERT.

* `Unchanged`: Nie trzeba zapisywać zmian w tej jednostce. Jednostka ma ten stan, gdy jest odczytywany z bazy danych.

* `Modified`: Zmodyfikowano niektóre lub wszystkie wartości właściwości jednostki. `SaveChanges`Metoda wystawia instrukcję Update.

* `Deleted`: Jednostka została oznaczona do usunięcia. `SaveChanges`Metoda wystawia instrukcję delete.

* `Detached`: Jednostka nie jest śledzona przez kontekst bazy danych.

W aplikacji klasycznej zmiany stanu są zazwyczaj ustawiane automatycznie. Odczytano jednostkę, wprowadzono zmiany, a stan jednostki jest automatycznie zmieniany na `Modified` . Wywołanie `SaveChanges` powoduje wygenerowanie instrukcji SQL Update, która aktualizuje tylko zmienione właściwości.

W aplikacji sieci Web, `DbContext` która odczytuje jednostkę i wyświetla dane są usuwane po wyrenderowaniu strony. Gdy `OnPostAsync` wywoływana jest metoda strony, tworzone jest nowe żądanie sieci Web i nowe wystąpienie `DbContext` . Odczytanie jednostki w tym nowym kontekście symuluje przetwarzanie pulpitu.

## <a name="update-the-delete-page"></a>Aktualizowanie strony usuwania

W tej sekcji zaimplementowano niestandardowy komunikat o błędzie, gdy wywołanie `SaveChanges` zakończy się niepowodzeniem.

Zastąp kod w obszarze *Pages/Students/Delete. cshtml. cs* poniższym kodem. Zmiany są wyróżnione (inne niż oczyszczanie `using` instrukcji).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

Poprzedni kod dodaje opcjonalny parametr `saveChangesError` do `OnGetAsync` sygnatury metody. `saveChangesError` wskazuje, czy metoda została wywołana po niepowodzeniu usunięcia obiektu studenta. Operacja usuwania może zakończyć się niepowodzeniem z powodu przejściowych problemów z siecią. Przejściowe błędy sieciowe są bardziej prawdopodobnie, gdy baza danych znajduje się w chmurze. `saveChangesError`Parametr ma wartość false, gdy strona usuwania `OnGetAsync` jest wywoływana z interfejsu użytkownika. Gdy `OnGetAsync` jest wywoływana przez `OnPostAsync` (ponieważ operacja usuwania nie powiodła się), `saveChangesError` parametr ma wartość true.

`OnPostAsync`Metoda pobiera wybraną jednostkę, a następnie wywołuje metodę [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) w celu ustawienia stanu jednostki na `Deleted` . Gdy `SaveChanges` jest wywoływana, generowane jest polecenie SQL Delete. W przypadku `Remove` niepowodzenia:

* Przechwycono wyjątek bazy danych.
* Metoda Delete Pages `OnGetAsync` jest wywoływana z `saveChangesError=true` .

Dodaj komunikat o błędzie do strony usuwania Razor (*strony/uczniowie/Delete. cshtml*):

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Uruchom aplikację i Usuń uczniów, aby przetestować stronę usuwania.

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/intro) 
>  [Następny samouczek](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku kod szkieletu CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie) zostanie sprawdzony i dostosowany.

Aby zminimalizować złożoność i utrzymywać te samouczki na EF Core, EF Core kod jest używany w modelach stron. Niektórzy Deweloperzy używają warstwy usług lub wzorca repozytorium w programie, aby utworzyć warstwę abstrakcji między interfejsem użytkownika ( Razor stronami) i warstwą dostępu do danych.

W tym samouczku zostaną zbadane strony tworzenie, edytowanie, usuwanie i szczegóły Razor w folderze *uczniów* .

Kod szkieletowy używa następującego wzorca do tworzenia, edytowania i usuwania stron:

* Pobieranie i wyświetlanie żądanych danych za pomocą metody HTTP GET `OnGetAsync` .
* Zapisz zmiany w danych za pomocą metody POST protokołu HTTP `OnPostAsync` .

Strony indeks i szczegóły pobierają i wyświetlają żądane dane za pomocą metody HTTP GET `OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync a FirstOrDefaultAsync

Wygenerowany kod używa [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), który jest ogólnie preferowany względem [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

 `FirstOrDefaultAsync` jest wydajniejsze niż `SingleOrDefaultAsync` podczas pobierania jednej jednostki:

* Chyba że kod musi sprawdzić, czy nie ma więcej niż jednej jednostki zwróconej przez zapytanie.
* `SingleOrDefaultAsync` Pobiera więcej danych i niezbędny przepływ pracy.
* `SingleOrDefaultAsync` zgłasza wyjątek, jeśli istnieje więcej niż jedna jednostka, która pasuje do części filtru.
* `FirstOrDefaultAsync` nie zgłasza, jeśli istnieje więcej niż jedna jednostka, która pasuje do części filtru.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

W większości kodu szkieletowego można używać [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) zamiast `FirstOrDefaultAsync` .

`FindAsync`:

* Znajduje jednostkę z kluczem podstawowym (PK). Jeśli jednostka z PK jest śledzona przez kontekst, jest zwracana bez żądania do bazy danych.
* Jest proste i zwięzłe.
* Jest zoptymalizowany pod kątem wyszukiwania pojedynczej jednostki.
* Mogą mieć w niektórych sytuacjach korzyści z wydajności, ale rzadko zdarzają się w przypadku typowych aplikacji sieci Web.
* Niejawnie używa [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) zamiast [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

Ale jeśli chcesz użyć `Include` innych jednostek, `FindAsync` nie jest już to konieczne. Oznacza to, że może być konieczne porzucenie `FindAsync` i przechodzenie do zapytania w miarę postępu Twojej aplikacji.

## <a name="customize-the-details-page"></a>Dostosuj stronę szczegółów

Przejdź do `Pages/Students` strony. Linki **Edytuj**, **szczegóły**i **Usuń** są generowane przez [pomocnika tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) w pliku *Pages/Students/index. cshtml* .

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Uruchom aplikację i wybierz łącze **szczegóły** . Adres URL ma postać `http://localhost:5000/Students/Details?id=2` . Identyfikator ucznia jest przenoszona przy użyciu ciągu zapytania ( `?id=2` ).

Zaktualizuj strony Edytuj, szczegóły i Usuń, Razor Aby użyć `"{id:int}"` szablonu trasy. Zmień dyrektywę Page dla każdej z tych stron z `@page` na `@page "{id:int}"` .

Żądanie do strony z szablonem trasy "{ID: int}", który **nie** zawiera wartości trasy całkowitej zwraca błąd HTTP 404 (nie znaleziono). Na przykład `http://localhost:5000/Students/Details` zwraca błąd 404. Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Uruchom aplikację, kliknij link szczegóły i sprawdź, czy adres URL przekazuje identyfikator jako dane trasy ( `http://localhost:5000/Students/Details/2` ).

Nie zmieniaj globalnie `@page` na, spowoduje to `@page "{id:int}"` przerwanie linków do domu i tworzenie stron.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Dodaj powiązane dane

Kod szkieletowy strony indeksu uczniów nie zawiera `Enrollments` właściwości. W tej sekcji zawartość `Enrollments` kolekcji zostanie wyświetlona na stronie szczegółów.

`OnGetAsync`Metoda *Pages/Students/details. cshtml. cs* używa `FirstOrDefaultAsync` metody do pobrania pojedynczej `Student` jednostki. Dodaj następujący wyróżniony kod:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

Metody [include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) i [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) powodują, że kontekst ładuje `Student.Enrollments` Właściwość nawigacji i w ramach każdej rejestracji `Enrollment.Course` właściwości nawigacji. Te metody są szczegółowo opisane w samouczku dotyczącym odczytywania danych.

Metoda [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) zwiększa wydajność w scenariuszach, gdy zwrócone jednostki nie są aktualizowane w bieżącym kontekście. `AsNoTracking` omówiono w dalszej części tego samouczka.

### <a name="display-related-enrollments-on-the-details-page"></a>Wyświetl powiązane rejestracje na stronie szczegółów

Otwórz *stronę/uczniów/szczegóły. cshtml*. Dodaj następujący wyróżniony kod, aby wyświetlić listę rejestracji:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Jeśli Wcięcie kodu jest nieprawidłowe po wklejeniu kodu, naciśnij klawisze CTRL-K-D, aby je poprawić.

Poprzedni kod pętle za pomocą jednostek we `Enrollments` właściwości nawigacji. Dla każdej rejestracji jest wyświetlany tytuł kursu i Klasa. Tytuł kursu jest pobierany z jednostki kursu przechowywanej we `Course` właściwości nawigacji jednostki rejestracji.

Uruchom aplikację, wybierz kartę **studenci** i kliknij link **szczegóły** dla ucznia. Zostanie wyświetlona lista kursów i ocen dla wybranego ucznia.

## <a name="update-the-create-page"></a>Aktualizowanie strony tworzenia

Zaktualizuj `OnPostAsync` metodę na *stronach/Students/Create. cshtml. cs* przy użyciu następującego kodu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Obejrzyj kod [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) :

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

W poprzednim kodzie program `TryUpdateModelAsync<Student>` próbuje zaktualizować obiekt, `emptyStudent` używając wartości ogłoszonych formularzy z właściwości [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) w [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel). `TryUpdateModelAsync` aktualizuje tylko wymienione właściwości ( `s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate` ).

W poprzednim przykładzie:

* Drugi argument ( `"student", // Prefix` ) jest prefiksem używanym do wyszukania wartości. Wielkość liter nie jest uwzględniana.
* Wartości opublikowanych formularzy są konwertowane na typy w `Student` modelu przy użyciu [powiązania modelu](xref:mvc/models/model-binding).

<a id="overpost"></a>

### <a name="overposting"></a>Przefinalizowanie

Używanie `TryUpdateModel` do aktualizowania pól z opublikowanymi wartościami jest najlepszym rozwiązaniem w zakresie zabezpieczeń, ponieważ uniemożliwia przekazanie. Załóżmy na przykład, że jednostka ucznia zawiera `Secret` Właściwość, której ta strona sieci Web nie powinna aktualizować ani dodawać:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Nawet jeśli aplikacja nie ma `Secret` pola na stronie Utwórz/zaktualizuj Razor , haker może ustawić wartość przez przepełnianie `Secret` . Haker może użyć narzędzia takiego jak programu Fiddler lub napisać kod JavaScript w celu opublikowania `Secret` wartości formularza. Oryginalny kod nie ogranicza pól używanych przez spinacz modelu podczas tworzenia wystąpienia ucznia.

Niezależnie od wartości, która hakera określona dla `Secret` pola formularza zostanie zaktualizowana w bazie danych. Na poniższej ilustracji przedstawiono Narzędzie programu Fiddler, które dodaje `Secret` pole (z wartością "naddawaj") do wartości zaksięgowanych formularzy.

![Programu Fiddler Dodawanie pola tajnego](../ef-mvc/crud/_static/fiddler.png)

Wartość "Zaksięguj" została pomyślnie dodana do `Secret` Właściwości wstawionego wiersza. Projektant aplikacji nigdy nie chce, `Secret` Aby właściwość została ustawiona na stronie Tworzenie.

<a name="vm"></a>

### <a name="view-model"></a>Wyświetlanie modelu

Model widoku zwykle zawiera podzbiór właściwości zawartych w modelu używanym przez aplikację. Model aplikacji jest często nazywany modelem domeny. Model domeny zwykle zawiera wszystkie właściwości wymagane przez odpowiednią jednostkę w bazie danych. Model widoku zawiera tylko właściwości, które są odpowiednie dla warstwy interfejsu użytkownika (na przykład Strona tworzenia). Poza modelem widoku niektóre aplikacje wykorzystują model powiązań lub model wejściowy do przekazywania danych między Razor klasą modelu strony stron i przeglądarką. Rozważmy następujący `Student` model widoku:

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Modele widoków zapewniają alternatywny sposób zapobiegania przepisywaniu. Model widoku zawiera tylko właściwości do wyświetlenia (Display) lub Update.

Poniższy kod używa `StudentVM` modelu widoku do utworzenia nowego ucznia:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Metoda [setValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) ustawia wartości tego obiektu, odczytując wartości z innego obiektu [propertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) . `SetValues` używa dopasowywania nazw właściwości. Typ modelu widoku nie musi być powiązany z typem modelu, dlatego musi mieć już pasujące właściwości.

Użycie `StudentVM` wymaga aktualizacji [CreateVM. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) `StudentVM` zamiast `Student` .

Na Razor stronach `PageModel` Klasa pochodna jest modelem widoku.

## <a name="update-the-edit-page"></a>Aktualizowanie strony edytowania

Zaktualizuj model strony dla strony Edycja. Najważniejsze zmiany są wyróżnione:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Zmiany w kodzie są podobne do strony tworzenie z kilkoma wyjątkami:

* `OnPostAsync` ma opcjonalny `id` parametr.
* Bieżący student jest pobierany z bazy danych, a nie od tworzenia pustego ucznia.
* `FirstOrDefaultAsync` został zastąpiony [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). `FindAsync` jest dobrym wyborem podczas wybierania jednostki z klucza podstawowego. Aby uzyskać więcej informacji, zobacz [FindAsync](#FindAsync) .

### <a name="test-the-edit-and-create-pages"></a>Testowanie stron Edycja i tworzenie

Utwórz i edytuj kilka jednostek ucznia.

## <a name="entity-states"></a>Stany jednostek

Kontekst bazy danych śledzi, czy jednostki w pamięci są zsynchronizowane z odpowiadającymi im wierszami w bazie danych. Informacje o synchronizacji kontekstu bazy danych określają, co się stanie w przypadku wywołania [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) . Na przykład gdy nowa jednostka jest przenoszona do metody [addasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) , stan tej jednostki jest ustawiany na wartość [dodana](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Gdy `SaveChangesAsync` jest wywoływana, kontekst bazy danych wystawia polecenie SQL INSERT.

Jednostka może być w jednym z [następujących stanów](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: Jednostka nie istnieje jeszcze w bazie danych. `SaveChanges`Metoda wystawia instrukcję INSERT.

* `Unchanged`: Nie trzeba zapisywać zmian w tej jednostce. Jednostka ma ten stan, gdy jest odczytywany z bazy danych.

* `Modified`: Zmodyfikowano niektóre lub wszystkie wartości właściwości jednostki. `SaveChanges`Metoda wystawia instrukcję Update.

* `Deleted`: Jednostka została oznaczona do usunięcia. `SaveChanges`Metoda wystawia instrukcję delete.

* `Detached`: Jednostka nie jest śledzona przez kontekst bazy danych.

W aplikacji klasycznej zmiany stanu są zazwyczaj ustawiane automatycznie. Odczytano jednostkę, wprowadzono zmiany i stan jednostki do automatycznego zmiany na `Modified` . Wywołanie `SaveChanges` powoduje wygenerowanie instrukcji SQL Update, która aktualizuje tylko zmienione właściwości.

W aplikacji sieci Web, `DbContext` która odczytuje jednostkę i wyświetla dane są usuwane po wyrenderowaniu strony. Gdy `OnPostAsync` wywoływana jest metoda strony, tworzone jest nowe żądanie sieci Web i nowe wystąpienie `DbContext` . Odczytanie jednostki w tym nowym kontekście symuluje przetwarzanie pulpitu.

## <a name="update-the-delete-page"></a>Aktualizowanie strony usuwania

W tej sekcji kod został dodany w celu zaimplementowania niestandardowego komunikatu o błędzie, gdy wywołanie `SaveChanges` zakończy się niepowodzeniem. Dodaj ciąg, aby zawierał możliwe komunikaty o błędach:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Zastąp metodę `OnGetAsync` poniższym kodem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Poprzedni kod zawiera opcjonalny parametr `saveChangesError` . `saveChangesError` wskazuje, czy metoda została wywołana po niepowodzeniu usunięcia obiektu studenta. Operacja usuwania może zakończyć się niepowodzeniem z powodu przejściowych problemów z siecią. Przejściowe błędy sieciowe są bardziej podobne do chmury. `saveChangesError`ma wartość false, gdy strona usuwania `OnGetAsync` jest wywoływana z interfejsu użytkownika. Gdy `OnGetAsync` jest wywoływana przez `OnPostAsync` (ponieważ operacja usuwania nie powiodła się), `saveChangesError` parametr ma wartość true.

### <a name="the-delete-pages-onpostasync-method"></a>Metoda Delete Pages OnPostAsync

Zamień na `OnPostAsync` następujący kod:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Powyższy kod pobiera wybraną jednostkę, a następnie wywołuje metodę [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) w celu ustawienia stanu jednostki na `Deleted` . Gdy `SaveChanges` jest wywoływana, generowane jest polecenie SQL Delete. W przypadku `Remove` niepowodzenia:

* Przechwycono wyjątek bazy danych.
* Metoda Delete Pages `OnGetAsync` jest wywoływana z `saveChangesError=true` .

### <a name="update-the-delete-no-locrazor-page"></a>Aktualizowanie strony usuwania Razor

Dodaj następujący wyróżniony komunikat o błędzie do Razor strony usuwania.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Test Delete.

## <a name="common-errors"></a>Typowe błędy

Studenci/indeks lub inne linki nie działają:

Sprawdź, czy Razor Strona zawiera poprawną `@page` dyrektywę. Na przykład strona studenci/indeks Razor **nie** powinna zawierać szablonu trasy:

```cshtml
@page "{id:int}"
```

Każda Razor Strona musi zawierać `@page` dyrektywę.



## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wersja tego samouczka usługi YouTube](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/intro) 
>  [Dalej](xref:data/ef-rp/sort-filter-page)

::: moniker-end
