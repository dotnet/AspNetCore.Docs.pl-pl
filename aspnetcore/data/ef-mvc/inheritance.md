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
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="95ec4-103">Samouczek: Implementowanie dziedziczenia - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="95ec4-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="95ec4-104">W poprzednim samouczku obsługiwane wyjątki współbieżności.</span><span class="sxs-lookup"><span data-stu-id="95ec4-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="95ec4-105">W tym samouczku pokazano, jak zaimplementować dziedziczenie w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="95ec4-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="95ec4-106">W programowaniu obiektowym można użyć dziedziczenia, aby ułatwić ponowne użycie kodu.</span><span class="sxs-lookup"><span data-stu-id="95ec4-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="95ec4-107">W tym samouczku zmienisz `Instructor` `Student` i klasy tak, aby `Person` pochodziły z klasy `LastName` podstawowej, która zawiera właściwości, takie jak te są wspólne dla instruktorów i studentów.</span><span class="sxs-lookup"><span data-stu-id="95ec4-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="95ec4-108">Nie będziesz dodawać ani zmieniać żadnych stron internetowych, ale zmienisz część kodu, a te zmiany zostaną automatycznie odzwierciedlone w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95ec4-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="95ec4-109">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="95ec4-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="95ec4-110">Mapowanie dziedziczenia do bazy danych</span><span class="sxs-lookup"><span data-stu-id="95ec4-110">Map inheritance to database</span></span>
> * <span data-ttu-id="95ec4-111">Tworzenie klasy Osoba</span><span class="sxs-lookup"><span data-stu-id="95ec4-111">Create the Person class</span></span>
> * <span data-ttu-id="95ec4-112">Aktualizuj instruktora i ucznia</span><span class="sxs-lookup"><span data-stu-id="95ec4-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="95ec4-113">Dodawanie osoby do modelu</span><span class="sxs-lookup"><span data-stu-id="95ec4-113">Add Person to the model</span></span>
> * <span data-ttu-id="95ec4-114">Tworzenie i aktualizowanie migracji</span><span class="sxs-lookup"><span data-stu-id="95ec4-114">Create and update migrations</span></span>
> * <span data-ttu-id="95ec4-115">Testowanie implementacji</span><span class="sxs-lookup"><span data-stu-id="95ec4-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="95ec4-116">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="95ec4-116">Prerequisites</span></span>

* [<span data-ttu-id="95ec4-117">Współbieżność uchwytów</span><span class="sxs-lookup"><span data-stu-id="95ec4-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="95ec4-118">Mapowanie dziedziczenia do bazy danych</span><span class="sxs-lookup"><span data-stu-id="95ec4-118">Map inheritance to database</span></span>

<span data-ttu-id="95ec4-119">I `Instructor` `Student` klasy w modelu danych szkoły mają kilka właściwości, które są identyczne:</span><span class="sxs-lookup"><span data-stu-id="95ec4-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Zajęcia dla uczniów i instruktorów](inheritance/_static/no-inheritance.png)

<span data-ttu-id="95ec4-121">Załóżmy, że chcesz wyeliminować nadmiarowy kod dla `Instructor` `Student` właściwości, które są współużytkowane przez i jednostek.</span><span class="sxs-lookup"><span data-stu-id="95ec4-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="95ec4-122">Lub chcesz napisać usługę, która może formatować nazwy bez dbania, czy nazwa pochodzi od instruktora lub ucznia.</span><span class="sxs-lookup"><span data-stu-id="95ec4-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="95ec4-123">Można utworzyć `Person` klasę podstawową, która zawiera tylko te `Instructor` `Student` właściwości udostępnione, a następnie sprawić, aby klasy i dziedziczyły z tej klasy podstawowej, jak pokazano na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="95ec4-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Zajęcia dla uczniów i instruktorów pochodzące z klasy person](inheritance/_static/inheritance.png)

<span data-ttu-id="95ec4-125">Istnieje kilka sposobów tej struktury dziedziczenia może być reprezentowana w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95ec4-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="95ec4-126">W jednej tabeli może być tabela Osoba zawierająca informacje o uczniach i instruktorach.</span><span class="sxs-lookup"><span data-stu-id="95ec4-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="95ec4-127">Niektóre kolumny mogą mieć zastosowanie tylko do instruktorów (HireDate), niektóre tylko do studentów (EnrollmentDate), niektóre do obu (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="95ec4-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="95ec4-128">Zazwyczaj kolumna rozróżniacza wskazuje, który typ reprezentuje każdy wiersz.</span><span class="sxs-lookup"><span data-stu-id="95ec4-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="95ec4-129">Na przykład kolumna dyskryminująca może mieć "Instruktor" dla instruktorów i "Student" dla studentów.</span><span class="sxs-lookup"><span data-stu-id="95ec4-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Przykład tabeli na hierarchię](inheritance/_static/tph.png)

<span data-ttu-id="95ec4-131">Ten wzorzec generowania struktury dziedziczenia jednostki z jednej tabeli bazy danych jest nazywany dziedziczeniem tabeli na hierarchię (TPH).</span><span class="sxs-lookup"><span data-stu-id="95ec4-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="95ec4-132">Alternatywą jest, aby baza danych wyglądać bardziej jak struktury dziedziczenia.</span><span class="sxs-lookup"><span data-stu-id="95ec4-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="95ec4-133">Na przykład można mieć tylko pola nazwy w tabeli Osoba i mieć oddzielne tabele Instruktor i Student z polami daty.</span><span class="sxs-lookup"><span data-stu-id="95ec4-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Dziedziczenie tabeli na typ](inheritance/_static/tpt.png)

<span data-ttu-id="95ec4-135">Ten wzorzec tworzenia tabeli bazy danych dla każdej klasy jednostki jest nazywany dziedziczeniem tabeli na typ (TPT).</span><span class="sxs-lookup"><span data-stu-id="95ec4-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="95ec4-136">Jeszcze inną opcją jest mapowanie wszystkich typów nieabstrakcyjnych do poszczególnych tabel.</span><span class="sxs-lookup"><span data-stu-id="95ec4-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="95ec4-137">Wszystkie właściwości klasy, w tym właściwości dziedziczone, są mapowane na kolumny odpowiedniej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95ec4-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="95ec4-138">Ten wzorzec jest nazywany table-per-concrete class (TPC) dziedziczenia.</span><span class="sxs-lookup"><span data-stu-id="95ec4-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="95ec4-139">Jeśli zaimplementowano dziedziczenie TPC dla klasy Person, Student i Instructor, jak pokazano wcześniej, tabele Uczeń i instruktor nie będą wyglądać inaczej po wdrożeniu dziedziczenia niż wcześniej.</span><span class="sxs-lookup"><span data-stu-id="95ec4-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="95ec4-140">Wzorce dziedziczenia TPC i TPH zazwyczaj zapewniają lepszą wydajność niż wzorce dziedziczenia TPT, ponieważ wzorce TPT mogą powodować złożone zapytania sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="95ec4-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="95ec4-141">W tym samouczku pokazano, jak zaimplementować dziedziczenie TPH.</span><span class="sxs-lookup"><span data-stu-id="95ec4-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="95ec4-142">TPH jest tylko wzorzec dziedziczenia, który obsługuje Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="95ec4-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="95ec4-143">Co zrobisz, to utworzyć `Person` klasę, `Instructor` zmienić i `Student` klasy `Person`pochodzić z , `DbContext`dodać nową klasę do , i utworzyć migrację.</span><span class="sxs-lookup"><span data-stu-id="95ec4-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="95ec4-144">Należy rozważyć zapisanie kopii projektu przed wprowadzeniem następujących zmian.</span><span class="sxs-lookup"><span data-stu-id="95ec4-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="95ec4-145">Następnie, jeśli napotkasz problemy i trzeba zacząć od nowa, łatwiej będzie rozpocząć od zapisanego projektu zamiast cofania kroków wykonanych dla tego samouczka lub wracając do początku całej serii.</span><span class="sxs-lookup"><span data-stu-id="95ec4-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="95ec4-146">Tworzenie klasy Osoba</span><span class="sxs-lookup"><span data-stu-id="95ec4-146">Create the Person class</span></span>

<span data-ttu-id="95ec4-147">W folderze Modele utwórz Person.cs i zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="95ec4-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="95ec4-148">Aktualizuj instruktora i ucznia</span><span class="sxs-lookup"><span data-stu-id="95ec4-148">Update Instructor and Student</span></span>

<span data-ttu-id="95ec4-149">W *Instructor.cs*, wyprowadz instructor klasy z Person klasy i usunąć klucz i nazwy pól.</span><span class="sxs-lookup"><span data-stu-id="95ec4-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="95ec4-150">Kod będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="95ec4-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="95ec4-151">Wprowadzać te same zmiany w *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="95ec4-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="95ec4-152">Dodawanie osoby do modelu</span><span class="sxs-lookup"><span data-stu-id="95ec4-152">Add Person to the model</span></span>

<span data-ttu-id="95ec4-153">Dodaj typ encji Osoba do *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="95ec4-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="95ec4-154">Nowe linie zostaną wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="95ec4-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="95ec4-155">Jest to wszystko, czego potrzebuje entity framework w celu skonfigurowania dziedziczenia tabeli na hierarchię.</span><span class="sxs-lookup"><span data-stu-id="95ec4-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="95ec4-156">Jak zobaczysz, po zaktualizowaniu bazy danych będzie miała tabelę Osoba zamiast tabely Uczeń i Instruktor.</span><span class="sxs-lookup"><span data-stu-id="95ec4-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="95ec4-157">Tworzenie i aktualizowanie migracji</span><span class="sxs-lookup"><span data-stu-id="95ec4-157">Create and update migrations</span></span>

<span data-ttu-id="95ec4-158">Zapisz swoje zmiany i zbuduj projekt.</span><span class="sxs-lookup"><span data-stu-id="95ec4-158">Save your changes and build the project.</span></span> <span data-ttu-id="95ec4-159">Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="95ec4-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="95ec4-160">Nie uruchamiaj `database update` jeszcze polecenia.</span><span class="sxs-lookup"><span data-stu-id="95ec4-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="95ec4-161">To polecenie spowoduje utratę danych, ponieważ spowoduje upuszczenie tabeli Instructor i zmianę nazwy tabeli Student na Osoba.</span><span class="sxs-lookup"><span data-stu-id="95ec4-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="95ec4-162">Należy podać kod niestandardowy, aby zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="95ec4-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="95ec4-163">Otwórz *migracje/\<sygnaturę czasową>_Inheritance.cs* i zastąp `Up` metodę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="95ec4-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="95ec4-164">Ten kod zajmuje się następującymi zadaniami aktualizacji bazy danych:</span><span class="sxs-lookup"><span data-stu-id="95ec4-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="95ec4-165">Usuwa ograniczenia klucza obcego i indeksy wskazujące tabelę Student.</span><span class="sxs-lookup"><span data-stu-id="95ec4-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="95ec4-166">Zmienia nazwę tabeli Instructor jako Person i wprowadza zmiany potrzebne do przechowywania danych ucznia:</span><span class="sxs-lookup"><span data-stu-id="95ec4-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="95ec4-167">Dodaje nullable EnrollmentDate dla studentów.</span><span class="sxs-lookup"><span data-stu-id="95ec4-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="95ec4-168">Dodaje kolumnę Rozróżniacz, aby wskazać, czy wiersz jest dla ucznia, czy instruktora.</span><span class="sxs-lookup"><span data-stu-id="95ec4-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="95ec4-169">Sprawia, że HireDate jest niemożna anulować, ponieważ wiersze uczniów nie będą miały dat zatrudnienia.</span><span class="sxs-lookup"><span data-stu-id="95ec4-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="95ec4-170">Dodaje tymczasowe pole, które będzie używane do aktualizowania kluczy obcych, które wskazują uczniów.</span><span class="sxs-lookup"><span data-stu-id="95ec4-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="95ec4-171">Podczas kopiowania uczniów do tabeli Osoba otrzymają nowe wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="95ec4-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="95ec4-172">Kopiuje dane z tabeli Student do tabeli Osoba.</span><span class="sxs-lookup"><span data-stu-id="95ec4-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="95ec4-173">Powoduje to, że uczniowie otrzymują przypisane nowe wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="95ec4-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="95ec4-174">Naprawia wartości klucza obcego, które wskazują na uczniów.</span><span class="sxs-lookup"><span data-stu-id="95ec4-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="95ec4-175">Ponownie tworzy ograniczenia klucza obcego i indeksy, teraz wskazując je na person tabeli.</span><span class="sxs-lookup"><span data-stu-id="95ec4-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="95ec4-176">(Jeśli identyfikator GUID zamiast liczby całkowitej jest typem klucza podstawowego, wartości klucza podstawowego ucznia nie musiałyby ulec zmianie, a kilka z tych kroków mogło zostać pominiętych).</span><span class="sxs-lookup"><span data-stu-id="95ec4-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="95ec4-177">Uruchom `database update` polecenie:</span><span class="sxs-lookup"><span data-stu-id="95ec4-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="95ec4-178">(W systemie produkcyjnym można wprowadzić `Down` odpowiednie zmiany do metody w przypadku, gdy kiedykolwiek trzeba było użyć, aby wrócić do poprzedniej wersji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95ec4-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="95ec4-179">W tym samouczku nie będzie `Down` przy użyciu metody.)</span><span class="sxs-lookup"><span data-stu-id="95ec4-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="95ec4-180">Istnieje możliwość uzyskania innych błędów podczas wprowadzania zmian schematu w bazie danych, która ma istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="95ec4-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="95ec4-181">Jeśli zostaną otrzymasz błędy migracji, których nie można rozwiązać, możesz zmienić nazwę bazy danych w ciągu połączenia lub usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95ec4-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="95ec4-182">W nowej bazie danych nie ma żadnych danych do migracji, a polecenie update-database jest bardziej prawdopodobne, aby zakończyć bez błędów.</span><span class="sxs-lookup"><span data-stu-id="95ec4-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="95ec4-183">Aby usunąć bazę danych, należy `database drop` użyć SSOX lub uruchom polecenie CLI.</span><span class="sxs-lookup"><span data-stu-id="95ec4-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="95ec4-184">Testowanie implementacji</span><span class="sxs-lookup"><span data-stu-id="95ec4-184">Test the implementation</span></span>

<span data-ttu-id="95ec4-185">Uruchom aplikację i wypróbuj różne strony.</span><span class="sxs-lookup"><span data-stu-id="95ec4-185">Run the app and try various pages.</span></span> <span data-ttu-id="95ec4-186">Wszystko działa tak samo jak wcześniej.</span><span class="sxs-lookup"><span data-stu-id="95ec4-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="95ec4-187">W **Eksploratorze obiektów programu SQL Server**rozwiń węzeł Połączenia **danych/Podręcznik szkolny,** a następnie tabele , a **tabele**Dla uczniów i instruktorów zostały zastąpione przez tabelę Osoba.</span><span class="sxs-lookup"><span data-stu-id="95ec4-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="95ec4-188">Otwórz projektanta tabel osoby i zobaczysz, że ma wszystkie kolumny, które były w tabelach Uczeń i instruktor.</span><span class="sxs-lookup"><span data-stu-id="95ec4-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Tabela osób w SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="95ec4-190">Kliknij prawym przyciskiem myszy tabelę Osoba, a następnie kliknij polecenie **Pokaż dane tabeli,** aby wyświetlić kolumnę dyskryminującą.</span><span class="sxs-lookup"><span data-stu-id="95ec4-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Tabela osób w SSOX - dane tabeli](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="95ec4-192">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="95ec4-192">Get the code</span></span>

[<span data-ttu-id="95ec4-193">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="95ec4-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="95ec4-194">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="95ec4-194">Additional resources</span></span>

<span data-ttu-id="95ec4-195">Aby uzyskać więcej informacji na temat dziedziczenia w core frameworku encji, zobacz [Dziedziczenie](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="95ec4-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="95ec4-196">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="95ec4-196">Next steps</span></span>

<span data-ttu-id="95ec4-197">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="95ec4-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="95ec4-198">Zamapowane dziedziczenie do bazy danych</span><span class="sxs-lookup"><span data-stu-id="95ec4-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="95ec4-199">Utworzono klasę Osoba</span><span class="sxs-lookup"><span data-stu-id="95ec4-199">Created the Person class</span></span>
> * <span data-ttu-id="95ec4-200">Zaktualizowany instruktor i uczeń</span><span class="sxs-lookup"><span data-stu-id="95ec4-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="95ec4-201">Dodano osobę do modelu</span><span class="sxs-lookup"><span data-stu-id="95ec4-201">Added Person to the model</span></span>
> * <span data-ttu-id="95ec4-202">Tworzenie i aktualizowanie migracji</span><span class="sxs-lookup"><span data-stu-id="95ec4-202">Created and update migrations</span></span>
> * <span data-ttu-id="95ec4-203">Przetestowano implementację</span><span class="sxs-lookup"><span data-stu-id="95ec4-203">Tested the implementation</span></span>

<span data-ttu-id="95ec4-204">Przejdź do następnego samouczka, aby dowiedzieć się, jak obsługiwać wiele stosunkowo zaawansowanych scenariuszy entity framework.</span><span class="sxs-lookup"><span data-stu-id="95ec4-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="95ec4-205">Dalej: Zaawansowane tematy</span><span class="sxs-lookup"><span data-stu-id="95ec4-205">Next: Advanced topics</span></span>](advanced.md)
