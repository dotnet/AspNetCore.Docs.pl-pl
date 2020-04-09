---
title: 'Samouczek: Implementowanie dziedziczenia - ASP.NET MVC z EF Core'
description: W tym samouczku pokazano, jak zaimplementować dziedziczenie w modelu danych przy użyciu entity framework core w aplikacji ASP.NET Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: dab3d2b057162f6d986db10e74e3681acc0ada3b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657242"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Samouczek: Implementowanie dziedziczenia - ASP.NET MVC z EF Core

W poprzednim samouczku obsługiwane wyjątki współbieżności. W tym samouczku pokazano, jak zaimplementować dziedziczenie w modelu danych.

W programowaniu obiektowym można użyć dziedziczenia, aby ułatwić ponowne użycie kodu. W tym samouczku zmienisz `Instructor` `Student` i klasy tak, aby `Person` pochodziły z klasy `LastName` podstawowej, która zawiera właściwości, takie jak te są wspólne dla instruktorów i studentów. Nie będziesz dodawać ani zmieniać żadnych stron internetowych, ale zmienisz część kodu, a te zmiany zostaną automatycznie odzwierciedlone w bazie danych.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Mapowanie dziedziczenia do bazy danych
> * Tworzenie klasy Osoba
> * Aktualizuj instruktora i ucznia
> * Dodawanie osoby do modelu
> * Tworzenie i aktualizowanie migracji
> * Testowanie implementacji

## <a name="prerequisites"></a>Wymagania wstępne

* [Współbieżność uchwytów](concurrency.md)

## <a name="map-inheritance-to-database"></a>Mapowanie dziedziczenia do bazy danych

I `Instructor` `Student` klasy w modelu danych szkoły mają kilka właściwości, które są identyczne:

![Zajęcia dla uczniów i instruktorów](inheritance/_static/no-inheritance.png)

Załóżmy, że chcesz wyeliminować nadmiarowy kod dla `Instructor` `Student` właściwości, które są współużytkowane przez i jednostek. Lub chcesz napisać usługę, która może formatować nazwy bez dbania, czy nazwa pochodzi od instruktora lub ucznia. Można utworzyć `Person` klasę podstawową, która zawiera tylko te `Instructor` `Student` właściwości udostępnione, a następnie sprawić, aby klasy i dziedziczyły z tej klasy podstawowej, jak pokazano na poniższej ilustracji:

![Zajęcia dla uczniów i instruktorów pochodzące z klasy person](inheritance/_static/inheritance.png)

Istnieje kilka sposobów tej struktury dziedziczenia może być reprezentowana w bazie danych. W jednej tabeli może być tabela Osoba zawierająca informacje o uczniach i instruktorach. Niektóre kolumny mogą mieć zastosowanie tylko do instruktorów (HireDate), niektóre tylko do studentów (EnrollmentDate), niektóre do obu (LastName, FirstName). Zazwyczaj kolumna rozróżniacza wskazuje, który typ reprezentuje każdy wiersz. Na przykład kolumna dyskryminująca może mieć "Instruktor" dla instruktorów i "Student" dla studentów.

![Przykład tabeli na hierarchię](inheritance/_static/tph.png)

Ten wzorzec generowania struktury dziedziczenia jednostki z jednej tabeli bazy danych jest nazywany dziedziczeniem tabeli na hierarchię (TPH).

Alternatywą jest, aby baza danych wyglądać bardziej jak struktury dziedziczenia. Na przykład można mieć tylko pola nazwy w tabeli Osoba i mieć oddzielne tabele Instruktor i Student z polami daty.

![Dziedziczenie tabeli na typ](inheritance/_static/tpt.png)

Ten wzorzec tworzenia tabeli bazy danych dla każdej klasy jednostki jest nazywany dziedziczeniem tabeli na typ (TPT).

Jeszcze inną opcją jest mapowanie wszystkich typów nieabstrakcyjnych do poszczególnych tabel. Wszystkie właściwości klasy, w tym właściwości dziedziczone, są mapowane na kolumny odpowiedniej tabeli. Ten wzorzec jest nazywany table-per-concrete class (TPC) dziedziczenia. Jeśli zaimplementowano dziedziczenie TPC dla klasy Person, Student i Instructor, jak pokazano wcześniej, tabele Uczeń i instruktor nie będą wyglądać inaczej po wdrożeniu dziedziczenia niż wcześniej.

Wzorce dziedziczenia TPC i TPH zazwyczaj zapewniają lepszą wydajność niż wzorce dziedziczenia TPT, ponieważ wzorce TPT mogą powodować złożone zapytania sprzężenia.

W tym samouczku pokazano, jak zaimplementować dziedziczenie TPH. TPH jest tylko wzorzec dziedziczenia, który obsługuje Entity Framework Core.  Co zrobisz, to utworzyć `Person` klasę, `Instructor` zmienić i `Student` klasy `Person`pochodzić z , `DbContext`dodać nową klasę do , i utworzyć migrację.

> [!TIP]
> Należy rozważyć zapisanie kopii projektu przed wprowadzeniem następujących zmian.  Następnie, jeśli napotkasz problemy i trzeba zacząć od nowa, łatwiej będzie rozpocząć od zapisanego projektu zamiast cofania kroków wykonanych dla tego samouczka lub wracając do początku całej serii.

## <a name="create-the-person-class"></a>Tworzenie klasy Osoba

W folderze Modele utwórz Person.cs i zastąp kod szablonu następującym kodem:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Aktualizuj instruktora i ucznia

W *Instructor.cs*, wyprowadz instructor klasy z Person klasy i usunąć klucz i nazwy pól. Kod będzie wyglądać następująco:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Wprowadzać te same zmiany w *Student.cs*.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Dodawanie osoby do modelu

Dodaj typ encji Osoba do *SchoolContext.cs*. Nowe linie zostaną wyróżnione.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

Jest to wszystko, czego potrzebuje entity framework w celu skonfigurowania dziedziczenia tabeli na hierarchię. Jak zobaczysz, po zaktualizowaniu bazy danych będzie miała tabelę Osoba zamiast tabely Uczeń i Instruktor.

## <a name="create-and-update-migrations"></a>Tworzenie i aktualizowanie migracji

Zapisz swoje zmiany i zbuduj projekt. Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenie:

```dotnetcli
dotnet ef migrations add Inheritance
```

Nie uruchamiaj `database update` jeszcze polecenia. To polecenie spowoduje utratę danych, ponieważ spowoduje upuszczenie tabeli Instructor i zmianę nazwy tabeli Student na Osoba. Należy podać kod niestandardowy, aby zachować istniejące dane.

Otwórz *migracje/\<sygnaturę czasową>_Inheritance.cs* i zastąp `Up` metodę następującym kodem:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Ten kod zajmuje się następującymi zadaniami aktualizacji bazy danych:

* Usuwa ograniczenia klucza obcego i indeksy wskazujące tabelę Student.

* Zmienia nazwę tabeli Instructor jako Person i wprowadza zmiany potrzebne do przechowywania danych ucznia:

* Dodaje nullable EnrollmentDate dla studentów.

* Dodaje kolumnę Rozróżniacz, aby wskazać, czy wiersz jest dla ucznia, czy instruktora.

* Sprawia, że HireDate jest niemożna anulować, ponieważ wiersze uczniów nie będą miały dat zatrudnienia.

* Dodaje tymczasowe pole, które będzie używane do aktualizowania kluczy obcych, które wskazują uczniów. Podczas kopiowania uczniów do tabeli Osoba otrzymają nowe wartości klucza podstawowego.

* Kopiuje dane z tabeli Student do tabeli Osoba. Powoduje to, że uczniowie otrzymują przypisane nowe wartości klucza podstawowego.

* Naprawia wartości klucza obcego, które wskazują na uczniów.

* Ponownie tworzy ograniczenia klucza obcego i indeksy, teraz wskazując je na person tabeli.

(Jeśli identyfikator GUID zamiast liczby całkowitej jest typem klucza podstawowego, wartości klucza podstawowego ucznia nie musiałyby ulec zmianie, a kilka z tych kroków mogło zostać pominiętych).

Uruchom `database update` polecenie:

```dotnetcli
dotnet ef database update
```

(W systemie produkcyjnym można wprowadzić `Down` odpowiednie zmiany do metody w przypadku, gdy kiedykolwiek trzeba było użyć, aby wrócić do poprzedniej wersji bazy danych. W tym samouczku nie będzie `Down` przy użyciu metody.)

> [!NOTE]
> Istnieje możliwość uzyskania innych błędów podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane. Jeśli zostaną otrzymasz błędy migracji, których nie można rozwiązać, możesz zmienić nazwę bazy danych w ciągu połączenia lub usunąć bazę danych. W nowej bazie danych nie ma żadnych danych do migracji, a polecenie update-database jest bardziej prawdopodobne, aby zakończyć bez błędów. Aby usunąć bazę danych, należy `database drop` użyć SSOX lub uruchom polecenie CLI.

## <a name="test-the-implementation"></a>Testowanie implementacji

Uruchom aplikację i wypróbuj różne strony. Wszystko działa tak samo jak wcześniej.

W **Eksploratorze obiektów programu SQL Server**rozwiń węzeł Połączenia **danych/Podręcznik szkolny,** a następnie tabele , a **tabele**Dla uczniów i instruktorów zostały zastąpione przez tabelę Osoba. Otwórz projektanta tabel osoby i zobaczysz, że ma wszystkie kolumny, które były w tabelach Uczeń i instruktor.

![Tabela osób w SSOX](inheritance/_static/ssox-person-table.png)

Kliknij prawym przyciskiem myszy tabelę Osoba, a następnie kliknij polecenie **Pokaż dane tabeli,** aby wyświetlić kolumnę dyskryminującą.

![Tabela osób w SSOX - dane tabeli](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji na temat dziedziczenia w core frameworku encji, zobacz [Dziedziczenie](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Zamapowane dziedziczenie do bazy danych
> * Utworzono klasę Osoba
> * Zaktualizowany instruktor i uczeń
> * Dodano osobę do modelu
> * Tworzenie i aktualizowanie migracji
> * Przetestowano implementację

Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać wiele stosunkowo zaawansowanych scenariuszy entity framework.

> [!div class="nextstepaction"]
> [Dalej: Zaawansowane tematy](advanced.md)
