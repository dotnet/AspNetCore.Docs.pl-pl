---
title: 'Samouczek: Korzystanie z funkcji migracji - ASP.NET MVC z EF Core'
description: W tym samouczku można rozpocząć korzystanie z funkcji migracji EF Core do zarządzania zmianami modelu danych w aplikacji ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 8b3417205457a5ce5fa16994701a06e2a4d7d350
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665726"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Samouczek: Korzystanie z funkcji migracji - ASP.NET MVC z EF Core

W tym samouczku można rozpocząć korzystanie z funkcji migracji ef core do zarządzania zmianami modelu danych. W późniejszych samouczkach dodasz więcej migracji podczas zmiany modelu danych.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dowiedz się więcej o migracjach
> * Zmienianie ciągu połączenia
> * Tworzenie migracji początkowej
> * Sprawdzanie metod w górę i w dół
> * Dowiedz się więcej o migawce modelu danych
> * Stosowanie migracji

## <a name="prerequisites"></a>Wymagania wstępne

* [Sortowanie, filtrowanie i stronicowanie](sort-filter-page.md)

## <a name="about-migrations"></a>Informacje o migracjach

Podczas tworzenia nowej aplikacji, model danych zmienia się często i za każdym razem, gdy zmienia się model, pobiera zsynchronizowane z bazą danych. Rozpoczęto te samouczki, konfigurując entity framework do tworzenia bazy danych, jeśli nie istnieje. Następnie za każdym razem, gdy zmienisz model danych - dodaj, usuń lub zmień klasy jednostek lub zmień klasę DbContext - możesz usunąć bazę danych, a EF utworzy nową, która pasuje do modelu, i zasiewa ją danymi testowymi.

Ta metoda utrzymywania bazy danych w synchronizacji z modelem danych działa dobrze, dopóki nie wdrożysz aplikacji w procesach produkcyjnych. Gdy aplikacja jest uruchomiona w produkcji jest zwykle przechowywanie danych, które chcesz zachować i nie chcesz stracić wszystko za każdym razem, gdy wprowadzasz zmiany, takie jak dodawanie nowej kolumny. Funkcja Migracji rdzenia EF rozwiązuje ten problem, umożliwiając EF zaktualizować schemat bazy danych zamiast tworzenia nowej bazy danych.

Aby pracować z migracjami, można użyć **konsoli Menedżera pakietów** (PMC) lub interfejsu wiersza polecenia.  Te samouczki pokazują, jak używać poleceń interfejsu wiersza polecenia. Informacje o PMC znajduje [się na końcu tego samouczka](#pmc).

## <a name="change-the-connection-string"></a>Zmienianie ciągu połączenia

W pliku *appsettings.json* zmień nazwę bazy danych w ciągu połączenia na ContosoUniversity2 lub inną nazwę, która nie została użyta na komputerze, którego używasz.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Ta zmiana konfiguruje projekt, tak aby pierwsza migracja utworzyła nową bazę danych. Nie jest to wymagane, aby rozpocząć migracje, ale zobaczysz później, dlaczego jest to dobry pomysł.

> [!NOTE]
> Jako alternatywę dla zmiany nazwy bazy danych można usunąć bazę danych. Użyj **programu SQL Server Object Explorer** `database drop` (SSOX) lub polecenia CLI:
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> W poniższej sekcji wyjaśniono, jak uruchomić polecenia interfejsu wiersza polecenia.

## <a name="create-an-initial-migration"></a>Tworzenie migracji początkowej

Zapisz swoje zmiany i zbuduj projekt. Następnie otwórz okno polecenia i przejdź do folderu projektu. Oto szybki sposób, aby to zrobić:

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Otwórz folder w Eksploratorze plików** z menu kontekstowego.

  ![Otwórz w eksploratorze plików element menu](migrations/_static/open-in-file-explorer.png)

* Wprowadź "cmd" na pasku adresu i naciśnij klawisz Enter.

  ![Otwórz okno polecenia](migrations/_static/open-command-window.png)

W oknie polecenia wprowadź następujące polecenie:

```dotnetcli
dotnet ef migrations add InitialCreate
```

W oknie polecenia widoczne są następujące dane wyjściowe:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat o błędzie *Brak pliku wykonywalnego znaleziono pasujące polecenie "dotnet-ef",* zobacz ten wpis w [blogu,](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) aby uzyskać pomoc w rozwiązywaniu problemów.

Jeśli zostanie wyświetlony komunikat o błędzie "*nie można uzyskać dostępu do pliku ... Plik ContosoUniversity.dll, ponieważ jest używany przez inny proces.*" znajdź ikonę IIS Express w zasobniku systemowym systemu Windows, a następnie kliknij ją prawym przyciskiem myszy, a następnie kliknij polecenie **ContosoUniversity > Zatrzymaj witrynę**.

## <a name="examine-up-and-down-methods"></a>Sprawdzanie metod w górę i w dół

Podczas wykonywania `migrations add` polecenia EF wygenerował kod, który utworzy bazę danych od podstaw. Ten kod znajduje się w folderze *Migracje,* w pliku o nazwie * \<sygnatura czasowa>_InitialCreate.cs*. Metoda `Up` `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom `Down` jednostek modelu danych, a metoda usuwa je, jak pokazano w poniższym przykładzie.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Migracje wywołuje `Up` metodę zaimplementowania zmian modelu danych dla migracji. Po wprowadzeniu polecenia, aby wycofać aktualizację, `Down` Migracje wywołuje metodę.

Ten kod jest przeznaczony dla migracji początkowej, `migrations add InitialCreate` która została utworzona po wprowadzeniu polecenia. Parametr nazwa migracji ("InitialCreate" w przykładzie) jest używany dla nazwy pliku i może być dowolny. Najlepiej wybrać słowo lub frazę, która podsumowuje to, co jest wykonywane podczas migracji. Na przykład można nazwać późniejszą migrację "AddDepartmentTable".

Jeśli migracja początkowa została utworzona, gdy baza danych już istnieje, kod tworzenia bazy danych jest generowany, ale nie musi być uruchamiany, ponieważ baza danych jest już zgodna z modelem danych. Po wdrożeniu aplikacji do innego środowiska, w którym baza danych jeszcze nie istnieje, ten kod zostanie uruchomiony w celu utworzenia bazy danych, więc warto najpierw ją przetestować. Dlatego wcześniej zmieniono nazwę bazy danych w ciągu połączenia , aby migracje mogły utworzyć nową od podstaw.

## <a name="the-data-model-snapshot"></a>Migawka modelu danych

Migracje tworzy *migawkę* bieżącego schematu bazy danych w *Migrations/SchoolContextModelSnapshot.cs*. Po dodaniu migracji EF określa, co się zmieniło, porównując model danych z plikiem migawki.

Użyj polecenia [usuwania migracji dotnet ef,](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) aby usunąć migrację. `dotnet ef migrations remove`usuwa migrację i zapewnia prawidłowe zresetowanie migawki. Jeśli `dotnet ef migrations remove` nie `dotnet ef migrations remove -v` powiedzie się, użyj, aby uzyskać więcej informacji na temat błędu.

Zobacz [EF Core migracji w środowiskach zespołu,](/ef/core/managing-schemas/migrations/teams) aby uzyskać więcej informacji na temat sposobu użycia pliku migawki.

## <a name="apply-the-migration"></a>Stosowanie migracji

W oknie polecenia wprowadź następujące polecenie, aby utworzyć w niej bazę danych i tabele.

```dotnetcli
dotnet ef database update
```

Dane wyjściowe z polecenia `migrations add` jest podobny do polecenia, z tą różnicą, że są widoczne dzienniki dla poleceń SQL, które skonfigurować bazę danych. Większość dzienników są pomijane w następujących danych wyjściowych przykładu. Jeśli nie chcesz, aby ten poziom szczegółowości w komunikatach dziennika, można zmienić poziom dziennika w *appsettings. Development.json.* Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

Użyj **Eksploratora obiektów programu SQL Server,** aby sprawdzić bazę danych, tak jak w pierwszym samouczku.  Można zauważyć dodanie \_ \_TABELI EFMigrationsHistory, która przechowuje śledzenie, które migracje zostały zastosowane do bazy danych. Wyświetl dane w tej tabeli, a zobaczysz jeden wiersz dla pierwszej migracji. (Ostatni dziennik w poprzednim przykładzie wyjściowym interfejsu wiersza polecenia pokazuje insert instrukcji, która tworzy ten wiersz.)

Uruchom aplikację, aby sprawdzić, czy wszystko nadal działa tak samo jak poprzednio.

![Strona Indeks uczniów](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Porównaj CLI i PMC

Narzędzia EF do zarządzania migracjami są dostępne w poleceniach interfejsu wiersza polecenia .NET Core lub z poleceń cmdlet programu PowerShell w oknie konsoli programu Visual Studio **Package Manager** (PMC). Ten samouczek pokazuje, jak korzystać z interfejsu wiersza polecenia, ale można użyć PMC, jeśli wolisz.

Polecenia EF dla poleceń PMC znajdują się w pakiecie [Microsoft.EntityFrameworkCore.Tools.](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) Ten pakiet znajduje się w [metapakietie Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)więc nie trzeba dodawać odwołania do `Microsoft.AspNetCore.App`pakietu, jeśli aplikacja ma odwołanie do pakietu .

**Ważne:** Nie jest to ten sam pakiet, który instalujesz dla interfejsu wiersza polecenia, edytując plik *csproj.* Nazwa tego kończy się `Tools`na , w przeciwieństwie `Tools.DotNet`do nazwy pakietu CLI, która kończy się na .

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

Aby uzyskać więcej informacji na temat poleceń PMC, zobacz [Konsola Menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Następny krok

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dowiedz się więcej o migracjach
> * Dowiedz się więcej o pakietach migracji NuGet
> * Zmieniono parametry połączenia
> * Utworzono migrację początkową
> * Zbadane metody w górę i w dół
> * Dowiedz się więcej o migawce modelu danych
> * Zastosowano migrację

Przejdź do następnego samouczka, aby rozpocząć przeglądanie bardziej zaawansowanych tematów dotyczących rozszerzania modelu danych. Po drodze utworzysz i zastosujesz dodatkowe migracje.

> [!div class="nextstepaction"]
> [Tworzenie i stosowanie dodatkowych migracji](complex-data-model.md)
