
> [!NOTE]
> W tym samouczku użyjesz funkcji *migracji* Entity Framework Core, jeśli to możliwe. Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych. Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone. Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane. Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem. Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite. Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.
>
>Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie. Ponowne kompilowanie tabeli obejmuje:
>
>* Tworzenie nowej tabeli.
>* Kopiowanie danych ze starej tabeli do nowej tabeli.
>* Porzucenie starej tabeli.
>* Zmiana nazwy nowej tabeli.
>
>Więcej informacji zawierają następujące zasoby:
>
> * [Ograniczenia dostawcy bazy danych EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Dostosowywanie kodu migracji](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Wstępne wypełnianie danych](/ef/core/modeling/data-seeding)
  * [Instrukcja ALTER TABLE w programie SQLite](https://sqlite.org/lang_altertable.html)