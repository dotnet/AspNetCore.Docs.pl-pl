---
title: Samouczek 5. stosowanie migracji do przykładowej firmy Contoso University
description: Część 5 z serii samouczków z uczelnią w firmie Contoso. Użyj funkcji migracji EF Core, aby zarządzać zmianami modelu danych w aplikacji ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: aebbc3f29b0356c7993abd83869ab21d3613bf61
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589351"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a>Samouczek: część 5, stosowanie migracji do przykładu z uczelnią contoso

W tym samouczku Zacznij korzystać z funkcji migracji EF Core, aby zarządzać zmianami modelu danych. W kolejnych samouczkach dodasz więcej migracji w miarę zmieniania modelu danych.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Więcej informacji na temat migracji
> * Tworzenie początkowej migracji
> * Sprawdzanie metod w górę i w dół
> * Informacje o migawce modelu danych
> * Zastosuj migrację

## <a name="prerequisites"></a>Wymagania wstępne

* [Sortowanie, filtrowanie i stronicowanie](sort-filter-page.md)

## <a name="about-migrations"></a>Informacje o migracjach

Podczas tworzenia nowej aplikacji model danych jest często zmieniany, a przy każdym zmianie modelu jest on synchronizowany z bazą danych. Te samouczki zostały rozpoczęte przez skonfigurowanie Entity Framework w celu utworzenia bazy danych, jeśli nie istnieje. Następnie za każdym razem, gdy zmieniasz model danych — Dodaj, Usuń lub Zmień klasy jednostek lub Zmień klasę DbContext — możesz usunąć bazę danych i EF tworzy nową, która jest zgodna z modelem, i wydawaj ją z danymi testowymi.

Ta metoda zachowania synchronizacji bazy danych z modelem danych działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym. Gdy aplikacja jest uruchomiona w środowisku produkcyjnym, zazwyczaj przechowuje dane, które mają być zachowane, i nie ma potrzeby utraty wszystkiego przy każdym wprowadzeniu zmiany, takiej jak dodanie nowej kolumny. Funkcja migracji EF Core rozwiązuje ten problem przez włączenie programu EF w celu zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.

Aby móc korzystać z migracji, można użyć **konsoli Menedżera pakietów** (PMC) lub interfejsu wiersza polecenia.  W tych samouczkach pokazano, jak używać poleceń interfejsu wiersza polecenia. Informacje na temat obiektu PMC są na [końcu tego samouczka](#pmc).

## <a name="drop-the-database"></a>Porzuć bazę danych

Zainstaluj EF Core narzędzia jako [Narzędzie globalne](/ef/core/miscellaneous/cli/dotnet) i Usuń bazę danych:

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

W poniższej sekcji opisano sposób uruchamiania poleceń interfejsu wiersza polecenia.

## <a name="create-an-initial-migration"></a>Tworzenie początkowej migracji

Zapisz zmiany i skompiluj projekt. Następnie otwórz okno polecenia i przejdź do folderu projektu. Oto szybka metoda:

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Otwórz folder w Eksploratorze plików** z menu kontekstowego.

  ![Otwórz w elemencie menu Eksploratora plików](migrations/_static/open-in-file-explorer.png)

* Wprowadź ciąg "cmd" na pasku adresu i naciśnij klawisz ENTER.

  ![Otwórz okno polecenia](migrations/_static/open-command-window.png)

Wprowadź następujące polecenie w oknie polecenia:

```dotnetcli
dotnet ef migrations add InitialCreate
```

W poprzednich poleceniach są wyświetlane dane wyjściowe podobne do następujących:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

Jeśli zostanie wyświetlony komunikat o błędzie "*nie można uzyskać dostępu do pliku... ContosoUniversity.dll, ponieważ jest on używany przez inny proces*", znajdź ikonę IIS Express na pasku zadań systemu Windows, a następnie kliknij ją prawym przyciskiem myszy, a następnie kliknij pozycję **ContosoUniversity > Zatrzymaj witrynę**.

## <a name="examine-up-and-down-methods"></a>Sprawdzanie metod w górę i w dół

Po wykonaniu `migrations add` polecenia EF wygenerowało kod, który spowoduje utworzenie bazy danych od podstaw. Ten kod znajduje się w folderze *migrations* w pliku o nazwie *\<timestamp> _InitialCreate. cs*. `Up`Metoda `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych, a `Down` Metoda usuwa je, jak pokazano w poniższym przykładzie.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Migracja wywołuje `Up` metodę w celu zaimplementowania zmian modelu danych dla migracji. Po wprowadzeniu polecenia w celu wycofania aktualizacji migracja wywołuje `Down` metodę.

Ten kod jest przeznaczony dla początkowej migracji, która została utworzona po wprowadzeniu `migrations add InitialCreate` polecenia. Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku i może być dowolnie wybrany. Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji. Można na przykład nazwać migrację do nowszej wersji "adddepartments".

W przypadku utworzenia początkowej migracji, gdy baza danych już istnieje, kod tworzenia bazy danych jest generowany, ale nie musi działać, ponieważ baza danych jest już zgodna z modelem danych. Po wdrożeniu aplikacji w innym środowisku, w którym baza danych jeszcze nie istnieje, ten kod zostanie uruchomiony w celu utworzenia bazy danych, więc dobrym pomysłem jest przetestowanie go w pierwszej kolejności. Z tego powodu zmieniono nazwę bazy danych w parametrach połączenia wcześniej — tak, aby migracje mogły utworzyć nową od podstaw.

## <a name="the-data-model-snapshot"></a>Migawka modelu danych

Migracja tworzy *migawkę* bieżącego schematu bazy danych w *migracji/SchoolContextModelSnapshot. cs*. Po dodaniu migracji, EF określa, co zmieniło się, porównując model danych z plikiem migawki.

Aby usunąć migrację, użyj polecenia [migracji programu dotnet EF Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) . `dotnet ef migrations remove` usuwa migrację i gwarantuje, że migawka zostanie prawidłowo zresetowana. Jeśli `dotnet ef migrations remove` to się nie powiedzie, użyj, `dotnet ef migrations remove -v` Aby uzyskać więcej informacji na temat błędu.

Aby uzyskać więcej informacji na temat sposobu użycia pliku migawek, zobacz [EF Core migracji w środowiskach zespołu](/ef/core/managing-schemas/migrations/teams) .

## <a name="apply-the-migration"></a>Zastosuj migrację

W oknie wiersza polecenia wprowadź następujące polecenie, aby utworzyć bazę danych i tabele.

```dotnetcli
dotnet ef database update
```

Dane wyjściowe polecenia są podobne do `migrations add` polecenia, z tą różnicą, że są wyświetlane dzienniki poleceń SQL, które konfigurują bazę danych. Większość dzienników zostanie pominiętych w następujących przykładowych danych wyjściowych. Jeśli wolisz, aby nie wyświetlać tego poziomu szczegółów w komunikatach dziennika, możesz zmienić poziom rejestrowania w *appsettings.Development.js* pliku. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
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
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

Użyj **Eksplorator obiektów SQL Server** , aby sprawdzić bazę danych zgodnie z pierwszym samouczkiem.  Zauważysz dodanie \_ \_ tabeli EFMigrationsHistory, która śledzi, które migracje zostały zastosowane do bazy danych. Wyświetl dane w tej tabeli i po pierwszej migracji zobaczysz jeden wiersz. (Ostatni dziennik w poprzednim przykładzie danych wyjściowych interfejsu wiersza polecenia przedstawia instrukcję INSERT, która tworzy ten wiersz).

Uruchom aplikację, aby upewnić się, że wszystko nadal działa tak samo jak wcześniej.

![Strona indeksu uczniów](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Porównanie interfejsu wiersza polecenia i kryteriów PMC

Narzędzia EF do zarządzania migracjami są dostępne z poleceń interfejs wiersza polecenia platformy .NET Core lub z poleceń cmdlet programu PowerShell w oknie **konsola Menedżera pakietów** programu Visual Studio (PMC). W tym samouczku pokazano, jak używać interfejsu wiersza polecenia, ale możesz użyć kryterium PMC, jeśli wolisz.

Polecenia EF dla poleceń PMC znajdują się w pakiecie [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) . Ten pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), dlatego nie trzeba dodawać odwołania do pakietu, jeśli aplikacja zawiera odwołanie do pakietu `Microsoft.AspNetCore.App` .

**Ważne:** To nie jest ten sam pakiet, który jest instalowany dla interfejsu wiersza polecenia, edytując plik *csproj* . Nazwa tego elementu zostanie zakończona, w `Tools` przeciwieństwie do nazwy pakietu interfejsu wiersza polecenia kończącego się na `Tools.DotNet` .

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).

Aby uzyskać więcej informacji na temat poleceń PMC, zobacz [konsola Menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a>Następny krok

Przejdź do następnego samouczka, aby rozpocząć bardziej zaawansowane tematy dotyczące rozszerzania modelu danych. W sposób tworzenia i stosowania dodatkowych migracji.

> [!div class="nextstepaction"]
> [Tworzenie i stosowanie dodatkowych migracji](complex-data-model.md)
