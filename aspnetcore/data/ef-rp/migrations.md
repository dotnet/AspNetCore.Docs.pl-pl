---
title: Część 4, Razor strony z EF Core w ASP.NET Core migracji
author: rick-anderson
description: Część 4 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061109"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a>Część 4 Razor strony z migracjami EF Core w programie ASP.NET Core

Autorzy [Dykstra](https://github.com/tdykstra), [Jan P Kowalski](https://twitter.com/thereformedprog)i [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

W tym samouczku przedstawiono funkcję migracji EF Core na potrzeby zarządzania zmianami modelu danych.

Po opracowaniu nowej aplikacji model danych zmienia się często. Za każdym razem, gdy model ulegnie zmianie, model nie jest zsynchronizowany z bazą danych. Ta seria samouczków została uruchomiona przez skonfigurowanie Entity Framework, aby utworzyć bazę danych, jeśli nie istnieje. Za każdym razem, gdy model danych ulegnie zmianie, należy usunąć bazę danych. Przy następnym uruchomieniu aplikacji wywołanie `EnsureCreated` ponownego tworzenia bazy danych jest zgodne z nowym modelem danych. `DbInitializer`Następnie zostanie uruchomiona nowa baza danych.

Takie podejście do utrzymywania synchronizacji bazy danych z modelem danych działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym. Gdy aplikacja działa w środowisku produkcyjnym, zazwyczaj przechowuje dane, które muszą zostać zachowane. Aplikacja nie może rozpocząć pracy z testową bazą danych przy każdej zmianie (na przykład dodanie nowej kolumny). Funkcja migracji EF Core rozwiązuje ten problem przez włączenie EF Core do zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.

Zamiast upuszczania i ponownego tworzenia bazy danych, gdy zmieni się model danych, migracja aktualizuje schemat i zachowuje istniejące dane.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Porzuć bazę danych

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Użyj **Eksplorator obiektów SQL Server** (SSOX), aby usunąć bazę danych, lub uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uruchom następujące polecenie w wierszu polecenia, aby zainstalować program EF CLI:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* W wierszu polecenia przejdź do folderu projektu. Folder projektu zawiera plik *ContosoUniversity. csproj* .

* Usuń plik *cu. DB* lub uruchom następujące polecenie:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Tworzenie początkowej migracji

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uruchom następujące polecenia w obszarze PMC:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Upewnij się, że wiersz polecenia znajduje się w folderze projektu, i uruchom następujące polecenia:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Metody w górę i w dół

EF Core `migrations add` polecenie wygenerowało kod, aby utworzyć bazę danych. Ten kod migracji znajduje się w pliku *migrations \<timestamp> _InitialCreate. cs* . `Up`Metoda `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych. `Down`Metoda usuwa je, jak pokazano w następującym przykładzie:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Poprzedni kod jest przeznaczony dla początkowej migracji. Kod:

* Zostało wygenerowane przez `migrations add InitialCreate` polecenie. 
* Jest wykonywane przez `database update` polecenie.
* Tworzy bazę danych dla modelu danych określonego przez klasę kontekstu bazy danych.

Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku. Nazwa migracji może być dowolną prawidłową nazwą pliku. Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji. Na przykład migracja dodana do tabeli działu może być nazywana "adddepartments".

## <a name="the-migrations-history-table"></a>Tabela historii migracji

* Aby sprawdzić bazę danych, użyj SSOX lub narzędzia SQLite.
* Zwróć uwagę na dodanie `__EFMigrationsHistory` tabeli. W `__EFMigrationsHistory` tabeli znajdują się informacje o tym, które migracje zostały zastosowane do bazy danych programu.
* Wyświetlanie danych w `__EFMigrationsHistory` tabeli. Pokazuje jeden wiersz dla pierwszej migracji.

## <a name="the-data-model-snapshot"></a>Migawka modelu danych

Migracja tworzy *migawkę* bieżącego modelu danych w *migracji/SchoolContextModelSnapshot. cs* . Po dodaniu migracji, EF określa zmiany, porównując bieżący model danych z plikiem migawki.

Ponieważ plik migawek śledzi stan modelu danych, nie można usunąć migracji, usuwając `<timestamp>_<migrationname>.cs` plik. Aby wykonać kopię zapasową najnowszej migracji, należy użyć `migrations remove` polecenia. To polecenie usuwa migrację i gwarantuje, że migawka zostanie prawidłowo zresetowana. Aby uzyskać więcej informacji, zobacz [migracja programu dotnet EF Usuń](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Usuń EnsureCreated

Ta seria samouczków została uruchomiona przy użyciu `EnsureCreated` . `EnsureCreated` nie tworzy tabeli historii migracji i dlatego nie można jej używać z migracjami. Jest ona przeznaczona do testowania lub szybkiego tworzenia prototypów, w których baza danych została porzucona i wielokrotnie utworzona.

Od tego momentu samouczki będą korzystać z migracji.

W polu *Data/dbinitialize. cs* Dodaj komentarz do następującego wiersza:

```csharp
context.Database.EnsureCreated();
```
Uruchom aplikację i sprawdź, czy baza danych została zainicjowana.

## <a name="applying-migrations-in-production"></a>Stosowanie migracji w środowisku produkcyjnym

Zalecamy, aby aplikacje produkcyjne **nie** wywoływały [bazy danych. Przeprowadź migrację](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji. `Migrate` nie należy wywoływać z aplikacji wdrożonej w farmie serwerów. Jeśli aplikacja jest skalowana w poziomie wielu wystąpień serwera, trudno jest upewnić się, że aktualizacje schematu bazy danych nie występują z wielu serwerów lub powodują konflikt z dostępem do odczytu i zapisu.

Migracja bazy danych powinna odbywać się w ramach wdrożenia i w sposób kontrolowany. Podejścia do migracji produkcyjnej bazy danych obejmują:

* Używanie migracji do tworzenia skryptów SQL i korzystania ze skryptów SQL we wdrożeniu.
* Uruchamianie `dotnet ef database update` z poziomu kontrolowanego środowiska.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli aplikacja używa SQL Server LocalDB i wyświetla następujący wyjątek:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Rozwiązanie może być uruchamiane z `dotnet ef database update` wiersza polecenia.

### <a name="additional-resources"></a>Dodatkowe zasoby

* [Interfejs wiersza polecenia EF Core](/ef/core/miscellaneous/cli/dotnet).
* [Konsola menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Następne kroki

Następny samouczek kompiluje model danych, dodając właściwości jednostki i nowe jednostki.

> [!div class="step-by-step"]
> [Poprzedni samouczek](xref:data/ef-rp/sort-filter-page) 
>  [Następny samouczek](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku zostanie użyta funkcja migracji EF Core do zarządzania zmianami modelu danych.

Jeśli wystąpią problemy, których nie można rozwiązać, Pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

Po opracowaniu nowej aplikacji model danych zmienia się często. Za każdym razem, gdy model ulegnie zmianie, model nie jest zsynchronizowany z bazą danych. Ten samouczek został uruchomiony przez skonfigurowanie Entity Framework, aby utworzyć bazę danych, jeśli nie istnieje. Za każdym razem, gdy zmienia się model danych:

* Baza danych została porzucona.
* EF tworzy nowy, który jest zgodny z modelem.
* Aplikacja wysienna bazę danych z danymi testowymi.

Takie podejście do synchronizowania bazy danych z modelem dane działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym. Gdy aplikacja działa w środowisku produkcyjnym, zazwyczaj przechowuje dane, które muszą zostać zachowane. Aplikacja nie może rozpoczynać się od bazy danych testów za każdym razem, gdy zostanie wprowadzona zmiana (na przykład dodanie nowej kolumny). Funkcja migracji EF Core rozwiązuje ten problem przez włączenie EF Core w celu zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.

Zamiast upuszczania i ponownego tworzenia bazy danych, gdy zmieni się model, migracja aktualizuje schemat i zachowuje istniejące dane.

## <a name="drop-the-database"></a>Porzuć bazę danych

Użyj **Eksplorator obiektów SQL Server** (SSOX) lub `database drop` polecenia:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W **konsoli Menedżera pakietów** (PMC) Uruchom następujące polecenie:

```powershell
Drop-Database
```

Uruchom `Get-Help about_EntityFrameworkCore` z PMC, aby uzyskać informacje pomocy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Otwórz okno polecenia i przejdź do folderu projektu. Folder projektu zawiera plik *Startup.cs* .

Wprowadź następujące polecenie w oknie polecenia:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Tworzenie początkowej migracji i aktualizowanie bazy danych

Kompilowanie projektu i Tworzenie pierwszej migracji.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

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

### <a name="examine-the-up-and-down-methods"></a>Sprawdzanie metod w górę i w dół

EF Core `migrations add` polecenie wygenerowało kod, aby utworzyć bazę danych. Ten kod migracji znajduje się w pliku *migrations \<timestamp> _InitialCreate. cs* . `Up`Metoda `InitialCreate` klasy tworzy tabele DB, które odpowiadają zestawom jednostek modelu danych. `Down`Metoda usuwa je, jak pokazano w następującym przykładzie:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Migracja wywołuje `Up` metodę w celu zaimplementowania zmian modelu danych dla migracji. Po wprowadzeniu polecenia w celu wycofania aktualizacji migracja wywołuje `Down` metodę.

Poprzedni kod jest przeznaczony dla początkowej migracji. Ten kod został utworzony, gdy `migrations add InitialCreate` polecenie zostało uruchomione. Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku. Nazwa migracji może być dowolną prawidłową nazwą pliku. Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji. Na przykład migracja dodana do tabeli działu może być nazywana "adddepartments".

Jeśli migracja początkowa jest tworzona i baza danych istnieje:

* Kod tworzenia bazy danych jest generowany.
* Nie trzeba uruchamiać kodu tworzenia bazy danych, ponieważ baza danych jest już zgodna z modelem danych. W przypadku uruchomienia kodu tworzenia bazy danych nie wprowadzono żadnych zmian, ponieważ baza danych jest już zgodna z modelem danych.

Gdy aplikacja zostanie wdrożona w nowym środowisku, należy uruchomić kod tworzenia bazy danych, aby utworzyć bazę danych.

Wcześniej baza danych została porzucona i nie istnieje, dlatego migracji tworzy nową bazę danych.

### <a name="the-data-model-snapshot"></a>Migawka modelu danych

Migracje tworzą *migawkę* bieżącego schematu bazy danych w *migracji/SchoolContextModelSnapshot. cs* . Po dodaniu migracji, EF określa, co zmieniło się, porównując model danych z plikiem migawki.

Aby usunąć migrację, użyj następującego polecenia:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove-Migration

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Aby uzyskać więcej informacji, zobacz [migracja programu dotnet EF Usuń](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Polecenie Usuń migracje powoduje usunięcie migracji i gwarantuje, że migawka zostanie prawidłowo zresetowana.

### <a name="remove-ensurecreated-and-test-the-app"></a>Usuń EnsureCreated i przetestuj aplikację

W przypadku wczesnego opracowywania programu `EnsureCreated` została użyta. W tym samouczku zostaną użyte migracje. `EnsureCreated` ma następujące ograniczenia:

* Pomija migracje i tworzy bazę danych i schemat.
* Nie tworzy tabeli migracji.
* *Nie* można go używać z migracjami.
* Jest przeznaczony do testowania lub szybkiego tworzenia prototypów, w których baza danych została porzucona i wielokrotnie utworzona.

Usuń `EnsureCreated` :

```csharp
context.Database.EnsureCreated();
```

Uruchom aplikację i sprawdź, czy baza danych została zainicjowana.

### <a name="inspect-the-database"></a>Inspekcja bazy danych

Użyj **Eksplorator obiektów SQL Server** , aby sprawdzić bazę danych. Zwróć uwagę na dodanie `__EFMigrationsHistory` tabeli. W `__EFMigrationsHistory` tabeli znajdują się informacje o tym, które migracje zostały zastosowane do bazy danych. Wyświetl dane w `__EFMigrationsHistory` tabeli, pokazując jeden wiersz dla pierwszej migracji. Ostatni dziennik w poprzednim przykładzie danych wyjściowych interfejsu wiersza polecenia przedstawia instrukcję INSERT, która tworzy ten wiersz.

Uruchom aplikację i sprawdź, czy wszystko działa.

## <a name="applying-migrations-in-production"></a>Stosowanie migracji w środowisku produkcyjnym

Zalecamy, aby aplikacje produkcyjne **nie** wywoływały metody [Database. migruje](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji. `Migrate` nie należy wywoływać z aplikacji w farmie serwerów. Na przykład jeśli aplikacja została wdrożona w chmurze przy użyciu skalowania w poziomie (uruchomiono wiele wystąpień aplikacji).

Migracja bazy danych powinna odbywać się w ramach wdrożenia i w sposób kontrolowany. Podejścia do migracji produkcyjnej bazy danych obejmują:

* Używanie migracji do tworzenia skryptów SQL i korzystania ze skryptów SQL we wdrożeniu.
* Uruchamianie `dotnet ef database update` z poziomu kontrolowanego środowiska.

EF Core używa `__MigrationsHistory` tabeli, aby sprawdzić, czy migracja musi być uruchomiona. Jeśli baza danych jest aktualna, migracja nie jest uruchamiana.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).

Aplikacja generuje następujący wyjątek:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Rozwiązanie: Uruchom `dotnet ef database update`

### <a name="additional-resources"></a>Dodatkowe zasoby

* [Wersja tego samouczka usługi YouTube](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [Interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).
* [Konsola menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Poprzedni](xref:data/ef-rp/sort-filter-page) 
>  [Dalej](xref:data/ef-rp/complex-data-model)

::: moniker-end

