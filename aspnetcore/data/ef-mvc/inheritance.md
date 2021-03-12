---
title: 'Samouczek: implementowanie dziedziczenia-ASP.NET MVC z EF Core'
description: W tym samouczku przedstawiono sposób implementowania dziedziczenia w modelu danych przy użyciu Entity Framework Core w aplikacji ASP.NET Core.
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 299e00b223d287c4e2ece3d1e250581e2a7565e5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586374"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Samouczek: implementowanie dziedziczenia-ASP.NET MVC z EF Core

W poprzednim samouczku zostały obsłużone wyjątki współbieżności. W tym samouczku pokazano, jak zaimplementować dziedziczenie w modelu danych.

W programowaniu zorientowanym obiektowo można użyć dziedziczenia, aby ułatwić ponowne użycie kodu. W tym samouczku zmienisz `Instructor` `Student` klasy i tak, aby dziedziczyli z `Person` klasy bazowej, która zawiera właściwości, takie jak te, `LastName` które są wspólne dla instruktorów i studentów. Nie dodasz ani nie zmienisz żadnych stron sieci Web, ale zmienisz część kodu, a zmiany zostaną automatycznie odzwierciedlone w bazie danych.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Dziedziczenie mapowania do bazy danych
> * Tworzenie klasy Person
> * Aktualizuj instruktora i uczniów
> * Dodawanie osoby do modelu
> * Tworzenie i aktualizowanie migracji
> * Testowanie implementacji

## <a name="prerequisites"></a>Wymagania wstępne

* [Obsługa współbieżności](concurrency.md)

## <a name="map-inheritance-to-database"></a>Dziedziczenie mapowania do bazy danych

`Instructor`Klasy i `Student` w modelu danych szkolnych mają kilka właściwości, które są identyczne:

![Klasy uczniów i instruktorów](inheritance/_static/no-inheritance.png)

Załóżmy, że chcesz wyeliminować nadmiarowy kod dla właściwości, które są współużytkowane `Instructor` przez `Student` obiekty i. Lub chcesz napisać usługę, która może formatować nazwy bez Caring, niezależnie od tego, czy nazwa pochodzi od instruktora, czy studenta. Można utworzyć `Person` klasę bazową, która zawiera tylko te właściwości udostępnione, a następnie uczynić `Instructor` `Student` klasy i dziedziczyć z tej klasy bazowej, jak pokazano na poniższej ilustracji:

![Klasy uczniów i instruktorów wyprowadzane z klasy Person](inheritance/_static/inheritance.png)

Istnieje kilka sposobów reprezentowania tej struktury dziedziczenia w bazie danych. Może istnieć tabela osób, która zawiera informacje o studentach i instruktorach w pojedynczej tabeli. Niektóre kolumny mogą dotyczyć tylko instruktorów (HireDate), niektórych tylko do uczniów (EnrollmentDate), niektórych do obu (LastName, FirstName). Zazwyczaj masz kolumnę rozróżniacza, aby wskazać, który typ reprezentuje każdy wiersz. Na przykład kolumna rozróżniacza może mieć "instruktor" dla instruktorów i "Student" dla studentów.

![Przykład tabeli na hierarchię](inheritance/_static/tph.png)

Ten wzorzec generowania struktury dziedziczenia jednostek z pojedynczej tabeli bazy danych jest nazywany dziedziczeniem na hierarchię (TPH).

Alternatywą jest, aby baza danych wyglądała podobnie jak struktura dziedziczenia. Na przykład można mieć tylko pola nazw w tabeli Person i mieć osobne tabele instruktorów i uczniów z polami daty.

> [!WARNING]
> Tabela na typ (TPT) nie jest obsługiwana przez EF Core 3. x, ale została zaimplementowana w [EF Core 5,0](/ef/core/what-is-new/ef-core-5.0/plan).

![Dziedziczenie dla typu tabeli](inheritance/_static/tpt.png)

Ten wzorzec tworzenia tabeli bazy danych dla każdej klasy jednostek jest nazywany dziedziczeniem tabeli na typ (TPT).

Jeszcze kolejną opcją jest zamapowanie wszystkich typów nieabstrakcyjnych na poszczególne tabele. Wszystkie właściwości klasy, łącznie z dziedziczonymi właściwościami, mapują do kolumn odpowiedniej tabeli. Ten wzorzec jest nazywany dziedziczeniem klasy opartej na tabeli (TPC). W przypadku zaimplementowania dziedziczenia testowego dla osoby, ucznia i klas instruktorów, jak pokazano wcześniej, w tabelach student i instruktor nie będą one wyglądały inaczej po wdrożeniu dziedziczenia niż wcześniej.

Wzorce TPHa i dziedziczenia zapewniają lepszą wydajność niż wzorce dziedziczenia TPT, ponieważ wzorce TPT mogą powodować złożone zapytania sprzężenia.

W tym samouczku pokazano, jak wdrożyć dziedziczenie TPH. TPH jest jedynym wzorcem dziedziczenia obsługiwanym przez Entity Framework Core.  To, co robisz, jest utworzenie `Person` klasy, zmiana `Instructor` klas i, `Student` Aby dziedziczyć z `Person` , dodać nową klasę do `DbContext` i utworzyć migrację.

> [!TIP]
> Rozważ zapisanie kopii projektu przed wprowadzeniem następujących zmian.  Jeśli wystąpią problemy i trzeba zacząć od nowa, będzie łatwiej zacząć od zapisanego projektu zamiast odwracania kroków wykonanych dla tego samouczka lub powrotu do początku całej serii.

## <a name="create-the-person-class"></a>Tworzenie klasy Person

W folderze modele Utwórz Person.cs i Zastąp kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Aktualizuj instruktora i uczniów

W *Instructor.cs*, pochodny klasy instruktora od klasy Person i Usuń pola klucza i nazwy. Kod będzie wyglądać podobnie do poniższego przykładu:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Wprowadź te same zmiany w programie *student.cs*.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Dodawanie osoby do modelu

Dodaj typ jednostki osoby do *SchoolContext.cs*. Nowe wiersze są wyróżnione.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

To wszystko, co Entity Framework potrzebuje w celu skonfigurowania dziedziczenia w hierarchii na poziomie tabeli. Jak widać, gdy baza danych zostanie zaktualizowana, będzie miała tabelę osób zamiast tabel uczniów i instruktorów.

## <a name="create-and-update-migrations"></a>Tworzenie i aktualizowanie migracji

Zapisz zmiany i skompiluj projekt. Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenie:

```dotnetcli
dotnet ef migrations add Inheritance
```

Nie uruchamiaj `database update` jeszcze polecenia. To polecenie spowoduje utratę danych, ponieważ spowoduje porzucenie tabeli instruktora i zmianę nazwy tabeli uczniów na osobę. Musisz podać niestandardowy kod, aby zachować istniejące dane.

Otwórz przystawkę *migracje/ \<timestamp> _Inheritance. cs* i Zastąp `Up` metodę następującym kodem:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Ten kod obejmuje następujące zadania aktualizacji bazy danych:

* Usuwa ograniczenia klucza obcego i indeksy wskazujące na tabelę uczniów.

* Zmienia nazwę tabeli instruktora jako osobę i wprowadza zmiany, które są niezbędne do przechowywania danych ucznia:

* Dodaje wartość null EnrollmentDate dla studentów.

* Dodaje kolumnę rozróżniacza, aby wskazać, czy wiersz dotyczy studenta, czy instruktora.

* Sprawia, że HireDate dopuszcza wartość null, ponieważ wiersze uczniów nie mają dat zatrudnienia.

* Dodaje pole tymczasowe, które będzie używane do aktualizacji kluczy obcych, które wskazują uczniów. W przypadku kopiowania uczniów do tabeli osób zostaną pobrane nowe wartości klucza podstawowego.

* Kopiuje dane z tabeli uczniów do tabeli Person. Powoduje to, że uczniowie mogą uzyskać przypisane nowe wartości klucza podstawowego.

* Naprawia wartości kluczy obcych, które wskazują uczniów.

* Ponownie tworzy ograniczenia klucza obcego i indeksy, teraz wskazując je do tabeli Person.

(Jeśli jako typ klucza podstawowego użyto identyfikatora GUID zamiast Integer, wartości klucza podstawowego studenta nie będą musiały ulec zmianie, a niektóre z tych kroków mogły zostać pominięte).

Uruchom `database update` polecenie:

```dotnetcli
dotnet ef database update
```

(W systemie produkcyjnym należy wprowadzić odpowiednie zmiany `Down` w metodzie w przypadku, gdy kiedykolwiek było konieczne użycie tego programu w celu powrotu do poprzedniej wersji bazy danych. Na potrzeby tego samouczka nie będziesz używać `Down` metody.)

> [!NOTE]
> Podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane, można uzyskać inne błędy. W przypadku wystąpienia błędów migracji, których nie można rozwiązać, można zmienić nazwę bazy danych w parametrach połączenia lub usunąć bazę danych. W przypadku nowej bazy danych nie ma żadnych danych do migracji, a polecenie Update-Database może być gotowe do ukończenia bez błędów. Aby usunąć bazę danych, należy użyć SSOX lub uruchomić `database drop` polecenie interfejsu wiersza polecenia.

## <a name="test-the-implementation"></a>Testowanie implementacji

Uruchom aplikację i wypróbuj różne strony. Wszystko działa tak samo jak wcześniej.

W **Eksplorator obiektów SQL Server** rozwiń węzeł **połączenia danych/SchoolContext** , a następnie **tabele** i sprawdź, czy tabele student i instruktor zostały zastąpione przez tabelę Person. Otwórz projektanta tabeli Person i sprawdź, czy zawiera on wszystkie kolumny, które zostały użyte w tabelach studenta i instruktora.

![Tabela osób w SSOX](inheritance/_static/ssox-person-table.png)

Kliknij prawym przyciskiem myszy tabelę osoba, a następnie kliknij polecenie **Pokaż dane tabeli** , aby wyświetlić kolumnę rozróżniacza.

![Tabela osób w SSOX — dane tabeli](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub Wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat dziedziczenia w Entity Framework Core, zobacz [dziedziczenie](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Mapowane dziedziczenie do bazy danych
> * Utworzono klasę Person
> * Zaktualizowany instruktor i student
> * Dodano osobę do modelu
> * Tworzenie i aktualizowanie migracji
> * Przetestowano implementację

Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać wiele relatywnie zaawansowanych scenariuszy Entity Framework.

> [!div class="nextstepaction"]
> [Dalej: Tematy zaawansowane](advanced.md)