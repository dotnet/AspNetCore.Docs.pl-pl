> [!NOTE]
> 
> **Ograniczenia SQLite**
>
> W tym samouczku używa funkcji *migracji* core programu Entity Framework, jeśli to możliwe. Migracje aktualizuje schemat bazy danych, aby dopasować zmiany w modelu danych. Jednak migracje tylko rodzaje zmian, które obsługuje aparat bazy danych i możliwości zmiany schematu SQLite są ograniczone. Na przykład dodawanie kolumny jest obsługiwane, ale usunięcie kolumny nie jest obsługiwane. Jeśli zostanie utworzona migracja w `ef migrations add` celu usunięcia kolumny, polecenie zakończy się pomyślnie, ale polecenie zakończy się niepowodzeniem. `ef database update` 
>
> Obejście ograniczeń SQLite jest ręczne pisanie kodu migracji do wykonywania przebudowy tabeli, gdy coś w tabeli zmienia. Kod będzie przejść `Up` w `Down` i metody migracji i obejmowałby:
>
> * Tworzenie nowej tabeli.
> * Kopiowanie danych ze starej tabeli do nowej tabeli.
> * Upuszczanie starej tabeli.
> * Zmiana nazwy nowej tabeli.
>
> Pisanie kodu specyficzne dla bazy danych tego typu jest poza zakresem tego samouczka. Zamiast tego ten samouczek porzuca i ponownie tworzy bazę danych, gdy próba zastosowania migracji zakończy się niepowodzeniem. Więcej informacji zawierają następujące zasoby:
>
> * [Ograniczenia dostawcy baz danych CORE FIRMY SQLite EF](/ef/core/providers/sqlite/limitations)
> * [Dostosowywanie kodu migracji](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Wysiew danych](/ef/core/modeling/data-seeding)
> * [Instrukcja TABELI SQLite ALTER](https://sqlite.org/lang_altertable.html)