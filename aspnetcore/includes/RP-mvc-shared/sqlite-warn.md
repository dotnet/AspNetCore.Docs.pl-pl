
> [!NOTE]
> W tym samouczku można użyć funkcji *migracji* core programu Entity Framework, jeśli to możliwe. Migracje aktualizuje schemat bazy danych, aby dopasować zmiany w modelu danych. Jednak migracje można wykonać tylko rodzaje zmian, które dostawca EF Core obsługuje, a możliwości dostawcy SQLite są ograniczone. Na przykład dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwana. Jeśli zostanie utworzona migracja w celu `ef migrations add` usunięcia lub zmiany `ef database update` kolumny, polecenie zakończy się pomyślnie, ale polecenie zakończy się niepowodzeniem. Ze względu na te ograniczenia w tym samouczku nie używa migracji dla zmian schematu SQLite. Zamiast tego po zmianie schematu można upuścić i ponownie utworzyć bazę danych.
>
>Obejście ograniczeń SQLite jest ręczne pisanie kodu migracji do wykonywania przebudowy tabeli, gdy coś w tabeli zmienia. Przebudowa tabeli obejmuje:
>
>* Tworzenie nowej tabeli.
>* Kopiowanie danych ze starej tabeli do nowej tabeli.
>* Upuszczanie starej tabeli.
>* Zmiana nazwy nowej tabeli.
>
>Więcej informacji zawierają następujące zasoby:
>
> * [Ograniczenia dostawcy baz danych CORE FIRMY SQLite EF](/ef/core/providers/sqlite/limitations)
> * [Dostosowywanie kodu migracji](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Wysiew danych](/ef/core/modeling/data-seeding)
  * [Instrukcja TABELI SQLite ALTER](https://sqlite.org/lang_altertable.html)