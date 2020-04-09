---
title: Strony brzytwy z EF Core w rdzeniu ASP.NET - Migracje - 4 z 8
author: rick-anderson
description: W tym samouczku można rozpocząć korzystanie z funkcji migracji EF Core do zarządzania zmianami modelu danych w aplikacji ASP.NET Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 86fd83c898fce8e121e4d259aaca12c59591e606
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656535"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a>Strony brzytwy z EF Core w rdzeniu ASP.NET - Migracje - 4 z 8

Przez [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)i Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku przedstawiono funkcję migracji ef core do zarządzania zmianami modelu danych.

Po opracowaniu nowej aplikacji model danych zmienia się często. Za każdym razem, gdy zmienia się model, model jest zsynchronizowany z bazą danych. Ta seria samouczków rozpoczęła się od skonfigurowania entity framework do utworzenia bazy danych, jeśli nie istnieje. Za każdym razem, gdy zmienia się model danych, należy upuścić bazę danych. Przy następnym uruchomieniu aplikacji wywołanie `EnsureCreated` ponownie tworzy bazę danych, aby dopasować nowy model danych. Klasa `DbInitializer` następnie uruchamia się do materiału siewnego nowej bazy danych.

Takie podejście do utrzymywania bazy danych w synchronizacji z modelem danych działa dobrze, dopóki nie wdrożysz aplikacji w procesach produkcyjnych. Gdy aplikacja jest uruchomiona w produkcji, zwykle przechowuje dane, które muszą być obsługiwane. Aplikacja nie może rozpocząć się od testowej bazy danych za każdym razem, gdy zostanie wniesiena zmiana (na przykład dodanie nowej kolumny). Funkcja Migracji rdzenia EF rozwiązuje ten problem, umożliwiając EF Core zaktualizować schemat bazy danych zamiast tworzyć nową bazę danych.

Zamiast upuszczania i ponownego tworzenia bazy danych po zmianie modelu danych, migracje aktualizuje schemat i zachowuje istniejące dane.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Upuść bazę danych

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Użyj **programu SQL Server Object Explorer** (SSOX), aby usunąć bazę danych, lub uruchom następujące polecenie w konsoli Menedżera **pakietów** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uruchom następujące polecenie w wierszu polecenia, aby zainstalować ef cli:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* W wierszu polecenia przejdź do folderu projektu. Folder projektu zawiera plik *ContosoUniversity.csproj.*

* Usuń plik *CU.db* lub uruchom następujące polecenie:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Tworzenie migracji początkowej

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Uruchom następujące polecenia w PMC:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Upewnij się, że wiersz polecenia znajduje się w folderze projektu i uruchom następujące polecenia:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Metody w górę i w dół

Polecenie EF `migrations add` Core wygenerowało kod do utworzenia bazy danych. Ten kod migracji znajduje się w pliku *sygnatury czasowej\<migracji>_InitialCreate.cs.* Metoda `Up` `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych. Metoda `Down` usuwa je, jak pokazano w poniższym przykładzie:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Poprzedni kod dotyczy migracji początkowej. Kod:

* Został wygenerowany `migrations add InitialCreate` przez polecenie. 
* Jest wykonywany `database update` przez polecenie.
* Tworzy bazę danych dla modelu danych określonego przez klasę kontekstu bazy danych.

Parametr nazwa migracji ("InitialCreate" w przykładzie) jest używany dla nazwy pliku. Nazwa migracji może być dowolną prawidłową nazwą pliku. Najlepiej wybrać słowo lub frazę, która podsumowuje to, co jest wykonywane podczas migracji. Na przykład migracji, która dodała tabelę działu może być nazywany "AddDepartmentTable."

## <a name="the-migrations-history-table"></a>Tabela historii migracji

* Użyj SSOX lub narzędzia SQLite, aby sprawdzić bazę danych.
* Zwróć uwagę na `__EFMigrationsHistory` dodanie tabeli. Tabela `__EFMigrationsHistory` śledzi, które migracje zostały zastosowane do bazy danych.
* Służy do wyświetlania danych w `__EFMigrationsHistory` tabeli. Pokazuje jeden wiersz dla pierwszej migracji.

## <a name="the-data-model-snapshot"></a>Migawka modelu danych

Migracje tworzy *migawkę* bieżącego modelu danych w *Migrations/SchoolContextModelSnapshot.cs*. Po dodaniu migracji EF określa, co się zmieniło, porównując bieżący model danych z plikiem migawki.

Ponieważ plik migawki śledzi stan modelu danych, nie można usunąć migracji, usuwając `<timestamp>_<migrationname>.cs` plik. Aby wycofać najnowszą migrację, `migrations remove` musisz użyć polecenia. To polecenie usuwa migrację i zapewnia poprawne zresetowanie migawki. Aby uzyskać więcej informacji, zobacz [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Usuń EnsureCreated

Ta seria samouczków `EnsureCreated`rozpoczęta przy użyciu programu . `EnsureCreated`nie tworzy tabeli historii migracji i dlatego nie można jej używać z migracjami. Jest przeznaczony do testowania lub szybkiego prototypowania, gdzie baza danych jest często odrzucana i często odtwarzana.

Od tego momentu samouczki będą używać migracji.

W *pliku Data/DBInitializer.cs*wyrzuć następujący wiersz:

```csharp
context.Database.EnsureCreated();
```
Uruchom aplikację i sprawdź, czy baza danych jest rozstawiona.

## <a name="applying-migrations-in-production"></a>Stosowanie migracji w produkcji

Zaleca się, że aplikacje produkcyjne **nie** [wywołać Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji. `Migrate`nie powinien być wywoływany z aplikacji, która jest wdrażana w farmie serwerów. Jeśli aplikacja jest skalowana w poziomie do wielu wystąpień serwera, trudno jest upewnić się, że aktualizacje schematu bazy danych nie nastąpi z wielu serwerów lub konflikt z dostępem do odczytu/zapisu.

Migracja bazy danych powinna być wykonywana w ramach wdrażania i w sposób kontrolowany. Metody migracji danych produkcyjnych obejmują:

* Używanie migracji do tworzenia skryptów SQL i używania skryptów SQL we wdrożeniu.
* Uruchamianie `dotnet ef database update` z kontrolowanego środowiska.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli aplikacja korzysta z usługi SQL Server LocalDB i wyświetla następujący wyjątek:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Rozwiązaniem może być `dotnet ef database update` uruchomienie w wierszu polecenia.

### <a name="additional-resources"></a>Zasoby dodatkowe

* [EF Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [Konsola menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Następne kroki

Następny samouczek tworzy model danych, dodając właściwości jednostki i nowe jednostki.

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/sort-filter-page)
> [Następny samouczek](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku używana jest funkcja migracji ef core do zarządzania zmianami modelu danych.

Jeśli napotkasz problemy, których nie możesz rozwiązać, pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

Po opracowaniu nowej aplikacji model danych zmienia się często. Za każdym razem, gdy zmienia się model, model jest zsynchronizowany z bazą danych. Ten samouczek rozpoczęty przez skonfigurowanie entity framework do tworzenia bazy danych, jeśli nie istnieje. Za każdym razem, gdy zmienia się model danych:

* Baza danych zostanie porzucona.
* EF tworzy nowy, który pasuje do modelu.
* Aplikacja nasiona DB z danymi testowymi.

Takie podejście do utrzymywania bazy danych w synchronizacji z modelem danych działa dobrze, dopóki nie wdrożysz aplikacji w procesach produkcyjnych. Gdy aplikacja jest uruchomiona w produkcji, zwykle przechowuje dane, które muszą być obsługiwane. Aplikacja nie może rozpocząć się od testowej bazy danych za każdym razem, gdy zostanie wniesiena zmiana (na przykład dodanie nowej kolumny). Funkcja Migracji rdzenia EF rozwiązuje ten problem, umożliwiając EF Core zaktualizować schemat bazy danych zamiast tworzyć nową bazę danych.

Zamiast upuszczania i ponownego tworzenia bazy danych po zmianie modelu danych, migracje aktualizuje schemat i zachowuje istniejące dane.

## <a name="drop-the-database"></a>Upuść bazę danych

Użyj **programu SQL Server Object Explorer** `database drop` (SSOX) lub polecenia:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:

```powershell
Drop-Database
```

Uruchom `Get-Help about_EntityFrameworkCore` z PMC, aby uzyskać informacje o pomocy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Otwórz okno polecenia i przejdź do folderu projektu. Folder projektu zawiera plik *Startup.cs.*

W oknie polecenia wprowadź następujące elementy:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Tworzenie migracji początkowej i aktualizowanie bazy danych

Skompiluj projekt i utwórz pierwszą migrację.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Sprawdzanie metod W górę i W dół

Polecenie EF `migrations add` Core wygenerowało kod do utworzenia bazy danych. Ten kod migracji znajduje się w pliku *sygnatury czasowej\<migracji>_InitialCreate.cs.* Metoda `Up` `InitialCreate` klasy tworzy tabele DB, które odpowiadają zestawom jednostek modelu danych. Metoda `Down` usuwa je, jak pokazano w poniższym przykładzie:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Migracje wywołuje `Up` metodę zaimplementowania zmian modelu danych dla migracji. Po wprowadzeniu polecenia, aby wycofać aktualizację, `Down` migracje wywołuje metodę.

Poprzedni kod dotyczy migracji początkowej. Ten kod został `migrations add InitialCreate` utworzony po uruchomieniu polecenia. Parametr nazwa migracji ("InitialCreate" w przykładzie) jest używany dla nazwy pliku. Nazwa migracji może być dowolną prawidłową nazwą pliku. Najlepiej wybrać słowo lub frazę, która podsumowuje to, co jest wykonywane podczas migracji. Na przykład migracji, która dodała tabelę działu może być nazywany "AddDepartmentTable."

Jeśli zostanie utworzona migracja początkowa i baza danych istnieje:

* Generowany jest kod tworzenia bazy danych.
* Kod tworzenia bazy danych nie musi być uruchamiany, ponieważ baza danych jest już zgodna z modelem danych. Jeśli kod tworzenia bazy danych jest uruchamiany, nie wprowadza żadnych zmian, ponieważ baza danych jest już zgodna z modelem danych.

Gdy aplikacja jest wdrażana w nowym środowisku, kod tworzenia bazy danych musi być uruchomiony w celu utworzenia bazy danych.

Wcześniej baza danych została porzucona i nie istnieje, więc migracje tworzy nowy baz danych.

### <a name="the-data-model-snapshot"></a>Migawka modelu danych

Migracje tworzą *migawkę* bieżącego schematu bazy danych w *programach Migrations/SchoolContextModelSnapshot.cs*. Po dodaniu migracji EF określa, co się zmieniło, porównując model danych z plikiem migawki.

Aby usunąć migrację, użyj następującego polecenia:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Usuń migrację

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Aby uzyskać więcej informacji, zobacz [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Polecenie usuń migracje usuwa migrację i zapewnia poprawne zresetowanie migawki.

### <a name="remove-ensurecreated-and-test-the-app"></a>Usuń EnsureCreated i przetestuj aplikację

Do wczesnego `EnsureCreated` rozwoju, był używany. W tym samouczku używane są migracje. `EnsureCreated`ma następujące ograniczenia:

* Pomija migracje i tworzy bazę danych i schemat.
* Nie tworzy tabeli migracji.
* *Nie* można używać z migracjami.
* Jest przeznaczony do testowania lub szybkiego prototypowania, gdzie baza danych jest często odrzucana i odtwarzana.

Usuń `EnsureCreated`:

```csharp
context.Database.EnsureCreated();
```

Uruchom aplikację i sprawdź, czy baza danych jest rozstawiona.

### <a name="inspect-the-database"></a>Sprawdzanie bazy danych

Użyj **Eksploratora obiektów programu SQL Server,** aby sprawdzić bazę danych. Zwróć uwagę na `__EFMigrationsHistory` dodanie tabeli. Tabela `__EFMigrationsHistory` śledzi, które migracje zostały zastosowane do bazy danych. Wyświetl dane w `__EFMigrationsHistory` tabeli, pokazuje jeden wiersz dla pierwszej migracji. Ostatni dziennik w poprzednim przykładzie wyjścia interfejsu wiersza polecenia pokazuje insert instrukcji, która tworzy ten wiersz.

Uruchom aplikację i sprawdź, czy wszystko działa.

## <a name="applying-migrations-in-production"></a>Stosowanie migracji w produkcji

Zaleca się, że aplikacje produkcyjne **nie** powinny wywoływać [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji. `Migrate`nie powinien być wywoływany z aplikacji w farmie serwerów. Na przykład jeśli aplikacja została wdrożona w chmurze ze skalą w poziomie (wiele wystąpień aplikacji są uruchomione).

Migracja bazy danych powinna być wykonywana w ramach wdrażania i w sposób kontrolowany. Metody migracji danych produkcyjnych obejmują:

* Używanie migracji do tworzenia skryptów SQL i używania skryptów SQL we wdrożeniu.
* Uruchamianie `dotnet ef database update` z kontrolowanego środowiska.

EF Core używa `__MigrationsHistory` tabeli, aby sprawdzić, czy wszelkie migracje muszą być uruchamiane. Jeśli baza danych jest aktualna, nie jest uruchamiana migracja.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).

Aplikacja generuje następujący wyjątek:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Rozwiązanie: Uruchom`dotnet ef database update`

### <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja tego samouczka w YouTube](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [Konsola menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/sort-filter-page)
> [następny](xref:data/ef-rp/complex-data-model)

::: moniker-end

