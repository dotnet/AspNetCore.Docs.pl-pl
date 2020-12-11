---
title: Razor Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8
author: rick-anderson
description: Pokazuje, jak utworzyć Razor aplikację stron przy użyciu Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 86b57a9cad27673b72ad174a18741f5528f9f78a
ms.sourcegitcommit: c321518bfe367280ef262aecaada287f17fe1bc5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011861"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="b3ded-103">Razor Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8</span><span class="sxs-lookup"><span data-stu-id="b3ded-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="b3ded-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b3ded-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b3ded-105">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b3ded-106">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="b3ded-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b3ded-107">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b3ded-108">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="b3ded-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b3ded-109">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="b3ded-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b3ded-110">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b3ded-111">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b3ded-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b3ded-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b3ded-112">Prerequisites</span></span>

* <span data-ttu-id="b3ded-113">Jeśli Razor dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do Razor stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b3ded-116">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-116">Database engines</span></span>

<span data-ttu-id="b3ded-117">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="b3ded-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b3ded-118">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b3ded-119">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="b3ded-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b3ded-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="b3ded-120">Troubleshooting</span></span>

<span data-ttu-id="b3ded-121">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b3ded-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b3ded-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="b3ded-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b3ded-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="b3ded-123">The sample app</span></span>

<span data-ttu-id="b3ded-124">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b3ded-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b3ded-125">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b3ded-126">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="b3ded-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="b3ded-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b3ded-130">Fokus samouczka polega na tym, jak używać EF Core z ASP.NET Core, a nie sposobu dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b3ded-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="b3ded-131">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="b3ded-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-132">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b3ded-133">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="b3ded-134">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="b3ded-135">W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `ContosoUniversity` w polu **Nazwa projektu**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="b3ded-136">Ważne jest, aby użyć tej dokładnej nazwy, z uwzględnieniem wielkich liter, więc każdy pasuje do tego, `namespace` kiedy kod jest kopiowany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="b3ded-137">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-137">Select **Create**.</span></span>
1. <span data-ttu-id="b3ded-138">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:</span><span class="sxs-lookup"><span data-stu-id="b3ded-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="b3ded-139">**Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="b3ded-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="b3ded-140">**ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="b3ded-141"> 
       Utwórz ![ Nowe okno dialogowe projektu ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="b3ded-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-143">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="b3ded-144">Uruchom następujące polecenia, aby utworzyć Razor Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b3ded-145">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="b3ded-145">Set up the site style</span></span>

<span data-ttu-id="b3ded-146">Skopiuj i wklej następujący kod do pliku *Pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b3ded-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="b3ded-147">Plik układu ustawia nagłówek, stopkę i menu witryny.</span><span class="sxs-lookup"><span data-stu-id="b3ded-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="b3ded-148">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="b3ded-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b3ded-149">Każde wystąpienie "ContosoUniversity" na "University-Uniwersytet".</span><span class="sxs-lookup"><span data-stu-id="b3ded-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b3ded-150">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-150">There are three occurrences.</span></span>
* <span data-ttu-id="b3ded-151">Wpisy menu **Home** i **privacy** są usuwane.</span><span class="sxs-lookup"><span data-stu-id="b3ded-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="b3ded-152">Wpisy są **dodawane do programu**, **studentów**, **kursów**, **instruktorów** i **działów**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="b3ded-153">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="b3ded-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="b3ded-154">Poprzedni kod zastępuje tekst dotyczący ASP.NET Core tekstem dla tej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="b3ded-155">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="b3ded-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b3ded-156">Model danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-156">The data model</span></span>

<span data-ttu-id="b3ded-157">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="b3ded-157">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="b3ded-159">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="b3ded-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b3ded-160">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="b3ded-160">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="b3ded-162">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="b3ded-163">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b3ded-164">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b3ded-165">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b3ded-166">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b3ded-167">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="b3ded-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b3ded-168">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b3ded-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b3ded-169">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="b3ded-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b3ded-170">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="b3ded-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b3ded-171">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b3ded-172">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="b3ded-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b3ded-173">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="b3ded-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b3ded-174">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="b3ded-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b3ded-175">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b3ded-176">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b3ded-177">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b3ded-178">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="b3ded-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b3ded-179">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="b3ded-179">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="b3ded-181">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b3ded-182">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b3ded-183">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b3ded-184">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="b3ded-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b3ded-185">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b3ded-186">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b3ded-187">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="b3ded-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b3ded-188">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="b3ded-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b3ded-189">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b3ded-190">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="b3ded-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b3ded-191">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b3ded-192">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="b3ded-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b3ded-193">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b3ded-194">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b3ded-195">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b3ded-196">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b3ded-197">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="b3ded-197">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="b3ded-199">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b3ded-200">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b3ded-201">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b3ded-202">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b3ded-203">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b3ded-204">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="b3ded-204">Scaffold Student pages</span></span>

<span data-ttu-id="b3ded-205">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="b3ded-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b3ded-206">Klasa EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="b3ded-207">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b3ded-208">Pochodzi od <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="b3ded-209">Razor strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="b3ded-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-210">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-211">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="b3ded-212">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b3ded-213">W oknie dialogowym **Dodaj nowy element szkieletowy** :</span><span class="sxs-lookup"><span data-stu-id="b3ded-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="b3ded-214">Na lewej karcie Wybierz pozycję **zainstalowane > typowe Razor strony >**</span><span class="sxs-lookup"><span data-stu-id="b3ded-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="b3ded-215">Wybierz **Razor strony przy użyciu Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="b3ded-216">Na **stronie Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="b3ded-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b3ded-217">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="b3ded-218">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="b3ded-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="b3ded-219">Zmień nazwę kontekstu danych na End `SchoolContext` zamiast `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="b3ded-220">Nazwa zaktualizowanego kontekstu: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="b3ded-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
    * <span data-ttu-id="b3ded-221">Wybierz pozycję **Dodaj** , aby zakończyć dodawanie klasy kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-221">Select **Add** to finish adding the data context class.</span></span>
   * <span data-ttu-id="b3ded-222">Wybierz pozycję **Dodaj** , aby zakończyć okno dialogowe **Dodawanie Razor stron** .</span><span class="sxs-lookup"><span data-stu-id="b3ded-222">Select **Add** to finish the **Add Razor Pages** dialog.</span></span>

<span data-ttu-id="b3ded-223">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="b3ded-223">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-225">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="b3ded-225">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="b3ded-226">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-226">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b3ded-227">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b3ded-227">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b3ded-228">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-228">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b3ded-229">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="b3ded-229">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="b3ded-230">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="b3ded-230">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b3ded-231">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="b3ded-231">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="b3ded-232">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="b3ded-232">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="b3ded-233">Jeśli poprzedni krok zakończy się niepowodzeniem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-233">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b3ded-234">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-234">The scaffolding process:</span></span>

* <span data-ttu-id="b3ded-235">Tworzy Razor strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="b3ded-235">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b3ded-236">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-236">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-237">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-237">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-238">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-238">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-239">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-239">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-240">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-240">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b3ded-241">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-241">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="b3ded-242">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-242">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="b3ded-243">Dodaje parametry połączenia z bazą danych do programu *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-243">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b3ded-244">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-244">Database connection string</span></span>

<span data-ttu-id="b3ded-245">Narzędzie do tworzenia szkieletu generuje parametry połączenia w *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="b3ded-245">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-246">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3ded-247">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="b3ded-247">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="b3ded-248">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="b3ded-248">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b3ded-249">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-249">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b3ded-251">Skróć parametry połączenia oprogramowania SQLite do *bazy danych cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="b3ded-251">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b3ded-252">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-252">Update the database context class</span></span>

<span data-ttu-id="b3ded-253">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-253">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b3ded-254">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b3ded-254">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b3ded-255">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-255">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b3ded-256">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-256">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b3ded-257">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-257">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b3ded-258">Poprzedni kod zmienia się z pojedynczej `DbSet<Student> Student` na plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-258">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="b3ded-259">Aby Razor kod stron był zgodny z nową `DBSet` nazwą, wprowadź globalną zmianę z: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="b3ded-259">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="b3ded-260">do `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="b3ded-260">to: `_context.Students.`</span></span>

<span data-ttu-id="b3ded-261">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b3ded-261">There are 8 occurrences.</span></span>

<span data-ttu-id="b3ded-262">Ponieważ zestaw jednostek zawiera wiele jednostek, wielu deweloperów preferuje `DBSet` nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="b3ded-262">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="b3ded-263">Wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="b3ded-263">The highlighted code:</span></span>

* <span data-ttu-id="b3ded-264">Tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-264">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b3ded-265">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="b3ded-265">In EF Core terminology:</span></span>
  * <span data-ttu-id="b3ded-266">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-266">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="b3ded-267">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-267">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="b3ded-268">Wywołania <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="b3ded-268">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="b3ded-269">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="b3ded-269">`OnModelCreating`:</span></span>
  * <span data-ttu-id="b3ded-270">Jest wywoływana, gdy została `SchoolContext` zainicjowana, ale zanim model zostanie zablokowany i użyty do zainicjowania kontekstu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-270">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="b3ded-271">Jest wymagany, ponieważ w dalszej części samouczka `Student` jednostka będzie miała odwołania do innych jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-271">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="b3ded-272">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b3ded-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b3ded-273">Startup.cs</span></span>

<span data-ttu-id="b3ded-274">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b3ded-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b3ded-275">Usługi takie jak `SchoolContext` są rejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-275">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="b3ded-276">Składniki, które wymagają tych usług, takie jak Razor strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-276">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="b3ded-277">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="b3ded-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b3ded-278">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="b3ded-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-279">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3ded-280">Program szkieleter dodał następujące wyróżnione wiersze:</span><span class="sxs-lookup"><span data-stu-id="b3ded-280">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b3ded-282">Sprawdź kod dodany przez wywołania szkieletu `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-282">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="b3ded-283">Aby uzyskać informacje na temat korzystania z produkcyjnej bazy danych, zobacz temat [Korzystanie z oprogramowania SQLite na potrzeby programowania. SQL Server do produkcji](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-283">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="b3ded-284">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b3ded-285">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="b3ded-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="b3ded-286">Dodaj filtr wyjątku bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-286">Add the database exception filter</span></span>

<span data-ttu-id="b3ded-287">Dodaj <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> do `ConfigureServices` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="b3ded-287">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-288">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-288">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="b3ded-289">Dodaj pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-289">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="b3ded-290">W obszarze PMC wprowadź następujące polecenie, aby dodać pakiet NuGet:</span><span class="sxs-lookup"><span data-stu-id="b3ded-290">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="b3ded-292">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`Pakiet NuGet zawiera ASP.NET Core oprogramowanie pośredniczące dla Entity Framework Core stron błędów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-292">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="b3ded-293">To oprogramowanie pośredniczące pomaga wykrywać i diagnozować błędy za pomocą migracji Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b3ded-293">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="b3ded-294">`AddDatabaseDeveloperPageExceptionFilter`Zapewnia przydatne informacje o błędzie w [środowisku programistycznym](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b3ded-294">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b3ded-295">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-295">Create the database</span></span>

<span data-ttu-id="b3ded-296">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="b3ded-296">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b3ded-297">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-297">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b3ded-298">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="b3ded-298">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b3ded-299">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="b3ded-299">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b3ded-300">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-300">Delete the database.</span></span> <span data-ttu-id="b3ded-301">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="b3ded-301">Any existing data is lost.</span></span>
* <span data-ttu-id="b3ded-302">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-302">Change the data model.</span></span> <span data-ttu-id="b3ded-303">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="b3ded-303">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b3ded-304">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-304">Run the app.</span></span>
* <span data-ttu-id="b3ded-305">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="b3ded-305">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b3ded-306">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-306">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b3ded-307">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="b3ded-307">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b3ded-308">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-308">When that is the case, use migrations.</span></span>

<span data-ttu-id="b3ded-309">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="b3ded-309">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b3ded-310">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-310">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b3ded-311">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="b3ded-311">Test the app</span></span>

* <span data-ttu-id="b3ded-312">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-312">Run the app.</span></span>
* <span data-ttu-id="b3ded-313">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-313">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b3ded-314">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="b3ded-314">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b3ded-315">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-315">Seed the database</span></span>

<span data-ttu-id="b3ded-316">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-316">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b3ded-317">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="b3ded-317">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b3ded-318">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-318">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b3ded-319">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-319">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b3ded-320">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-320">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b3ded-321">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="b3ded-321">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b3ded-322">W *program.cs* Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="b3ded-322">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-323">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-323">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3ded-324">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="b3ded-324">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="b3ded-325">Odpowiedz przy użyciu `Y` , aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-325">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-326">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-326">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-327">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-327">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b3ded-328">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-328">Restart the app.</span></span>
* <span data-ttu-id="b3ded-329">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-329">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b3ded-330">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-330">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-331">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-331">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-332">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b3ded-332">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b3ded-333">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-333">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="b3ded-334">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="b3ded-334">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b3ded-335">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="b3ded-335">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b3ded-336">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-336">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b3ded-337">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-337">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-338">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-338">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b3ded-339">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="b3ded-339">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b3ded-340">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-340">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b3ded-341">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="b3ded-341">Asynchronous code</span></span>

<span data-ttu-id="b3ded-342">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="b3ded-342">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b3ded-343">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="b3ded-343">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b3ded-344">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="b3ded-344">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b3ded-345">W przypadku kodu synchronicznego wiele wątków może zostać rozwiązanych, chociaż nie działają, ponieważ oczekują na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-345">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b3ded-346">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="b3ded-346">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b3ded-347">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="b3ded-347">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b3ded-348">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="b3ded-348">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b3ded-349">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="b3ded-349">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b3ded-350">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-350">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b3ded-351">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="b3ded-351">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b3ded-352">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="b3ded-352">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b3ded-353">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-353">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b3ded-354">`Task`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="b3ded-354">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="b3ded-355">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="b3ded-355">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b3ded-356">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-356">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b3ded-357">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-357">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b3ded-358">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-358">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b3ded-359">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="b3ded-359">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b3ded-360">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-360">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b3ded-361">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-361">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b3ded-362">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-362">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b3ded-363">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-363">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b3ded-364">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-364">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b3ded-365">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b3ded-365">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="b3ded-366">Zagadnienia dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="b3ded-366">Performance considerations</span></span>

<span data-ttu-id="b3ded-367">Ogólnie rzecz biorąc, Strona sieci Web nie powinna ładować dowolnej liczby wierszy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-367">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="b3ded-368">Zapytanie powinno używać podejścia do ograniczania.</span><span class="sxs-lookup"><span data-stu-id="b3ded-368">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="b3ded-369">Na przykład poprzednie zapytanie może użyć, `Take` Aby ograniczyć liczbę zwracanych wierszy:</span><span class="sxs-lookup"><span data-stu-id="b3ded-369">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b3ded-370">Wyliczenie dużej tabeli w widoku może zwrócić częściowo skonstruowaną odpowiedź HTTP 200, jeśli wyjątek bazy danych wystąpił częściowo przez wyliczenie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-370">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="b3ded-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> wartość domyślna to 1024.</span><span class="sxs-lookup"><span data-stu-id="b3ded-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="b3ded-372">Następujące zestawy kodu `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="b3ded-372">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b3ded-373">Stronicowanie jest omówione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="b3ded-373">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b3ded-374">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="b3ded-374">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b3ded-375">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="b3ded-375">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b3ded-376">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-376">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b3ded-377">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="b3ded-377">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b3ded-378">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-378">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b3ded-379">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="b3ded-379">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b3ded-380">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="b3ded-380">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b3ded-381">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-381">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b3ded-382">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b3ded-382">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b3ded-383">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b3ded-383">Prerequisites</span></span>

* <span data-ttu-id="b3ded-384">Jeśli Razor dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do Razor stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-384">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-385">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-385">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b3ded-387">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-387">Database engines</span></span>

<span data-ttu-id="b3ded-388">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="b3ded-388">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b3ded-389">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-389">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b3ded-390">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="b3ded-390">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b3ded-391">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="b3ded-391">Troubleshooting</span></span>

<span data-ttu-id="b3ded-392">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b3ded-392">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b3ded-393">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="b3ded-393">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b3ded-394">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="b3ded-394">The sample app</span></span>

<span data-ttu-id="b3ded-395">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b3ded-395">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b3ded-396">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-396">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b3ded-397">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="b3ded-397">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="b3ded-400">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-400">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b3ded-401">Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b3ded-401">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="b3ded-402">Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-402">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="b3ded-403">Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="b3ded-403">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="b3ded-404">Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-404">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-405">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-405">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3ded-406">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-406">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b3ded-407">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="b3ded-407">Build the project.</span></span>
* <span data-ttu-id="b3ded-408">W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="b3ded-408">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="b3ded-409">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-409">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b3ded-411">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-411">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b3ded-412">Usuń *ContosoUniversity. csproj* i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-412">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="b3ded-413">W *program.cs*, komentarz `#define Startup` `StartupSQLite` jest używany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-413">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="b3ded-414">Usuń *appSettings.js* i zmień nazwę *appSettingsSQLite.jsna* na *appSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-414">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="b3ded-415">Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-415">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="b3ded-416">Wykonaj wyszukiwanie globalne dla `#if SQLiteVersion` i Usuń `#if SQLiteVersion` oraz skojarzoną `#endif` instrukcję.</span><span class="sxs-lookup"><span data-stu-id="b3ded-416">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="b3ded-417">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="b3ded-417">Build the project.</span></span>
* <span data-ttu-id="b3ded-418">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="b3ded-418">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="b3ded-419">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="b3ded-419">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="b3ded-420">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-420">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="b3ded-421">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="b3ded-421">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-422">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-423">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-423">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b3ded-424">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-424">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b3ded-425">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-425">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="b3ded-426">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-426">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="b3ded-427">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-427">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-428">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-428">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-429">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-429">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="b3ded-430">Uruchom następujące polecenia, aby utworzyć Razor Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-430">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b3ded-431">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="b3ded-431">Set up the site style</span></span>

<span data-ttu-id="b3ded-432">Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b3ded-432">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="b3ded-433">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="b3ded-433">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b3ded-434">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-434">There are three occurrences.</span></span>

* <span data-ttu-id="b3ded-435">Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów**, **kursów**, **instruktorów** i **działów**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-435">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="b3ded-436">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="b3ded-436">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="b3ded-437">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b3ded-437">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="b3ded-438">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="b3ded-438">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b3ded-439">Model danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-439">The data model</span></span>

<span data-ttu-id="b3ded-440">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="b3ded-440">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="b3ded-442">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="b3ded-442">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b3ded-443">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="b3ded-443">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="b3ded-445">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-445">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="b3ded-446">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-446">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b3ded-447">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-447">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b3ded-448">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-448">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b3ded-449">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-449">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b3ded-450">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="b3ded-450">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b3ded-451">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b3ded-451">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b3ded-452">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="b3ded-452">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b3ded-453">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="b3ded-453">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b3ded-454">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-454">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b3ded-455">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="b3ded-455">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b3ded-456">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="b3ded-456">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b3ded-457">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="b3ded-457">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b3ded-458">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-458">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b3ded-459">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-459">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b3ded-460">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-460">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b3ded-461">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="b3ded-461">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b3ded-462">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="b3ded-462">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="b3ded-464">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-464">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b3ded-465">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-465">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b3ded-466">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-466">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b3ded-467">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="b3ded-467">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b3ded-468">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-468">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b3ded-469">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-469">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b3ded-470">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="b3ded-470">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b3ded-471">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="b3ded-471">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b3ded-472">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-472">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b3ded-473">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="b3ded-473">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b3ded-474">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-474">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b3ded-475">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="b3ded-475">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b3ded-476">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-476">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b3ded-477">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-477">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b3ded-478">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-478">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b3ded-479">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-479">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b3ded-480">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="b3ded-480">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="b3ded-482">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-482">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b3ded-483">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-483">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b3ded-484">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-484">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b3ded-485">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-485">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b3ded-486">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-486">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b3ded-487">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="b3ded-487">Scaffold Student pages</span></span>

<span data-ttu-id="b3ded-488">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="b3ded-488">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b3ded-489">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="b3ded-489">An EF Core *context* class.</span></span> <span data-ttu-id="b3ded-490">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-490">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b3ded-491">Pochodzi od `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-491">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="b3ded-492">Razor strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="b3ded-492">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-493">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-493">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-494">Utwórz folder *uczniów* w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-494">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="b3ded-495">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-495">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b3ded-496">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-496">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="b3ded-497">Na **stronie Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="b3ded-497">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b3ded-498">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-498">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="b3ded-499">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="b3ded-499">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="b3ded-500">Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-500">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="b3ded-501">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-501">Select **Add**.</span></span>

<span data-ttu-id="b3ded-502">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="b3ded-502">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-503">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-503">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-504">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="b3ded-504">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="b3ded-505">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-505">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b3ded-506">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b3ded-506">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b3ded-507">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-507">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b3ded-508">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="b3ded-508">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="b3ded-509">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="b3ded-509">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b3ded-510">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="b3ded-510">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="b3ded-511">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="b3ded-511">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="b3ded-512">Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-512">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b3ded-513">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-513">The scaffolding process:</span></span>

* <span data-ttu-id="b3ded-514">Tworzy Razor strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="b3ded-514">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b3ded-515">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-515">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-516">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-516">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-517">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-517">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-518">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-518">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b3ded-519">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-519">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b3ded-520">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-520">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="b3ded-521">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-521">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="b3ded-522">Dodaje parametry połączenia z bazą danych do programu *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-522">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b3ded-523">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-523">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-524">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-524">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3ded-525">*appsettings.json* Plik określa parametry połączenia [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="b3ded-525">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="b3ded-526">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="b3ded-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b3ded-527">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-527">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-528">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-528">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b3ded-529">Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="b3ded-529">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b3ded-530">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-530">Update the database context class</span></span>

<span data-ttu-id="b3ded-531">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-531">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b3ded-532">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b3ded-532">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b3ded-533">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-533">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b3ded-534">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-534">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b3ded-535">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-535">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b3ded-536">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-536">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b3ded-537">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="b3ded-537">In EF Core terminology:</span></span>

* <span data-ttu-id="b3ded-538">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-538">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="b3ded-539">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-539">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b3ded-540">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="b3ded-540">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="b3ded-541">Ponieważ narzędzie tworzenia szkieletów utworzyło `Student` nieogólnymi, ten krok zmienia go na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-541">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="b3ded-542">Aby Razor kod stron był zgodny z nową nazwą nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-542">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="b3ded-543">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="b3ded-543">There are 8 occurrences.</span></span>

<span data-ttu-id="b3ded-544">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-544">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b3ded-545">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b3ded-545">Startup.cs</span></span>

<span data-ttu-id="b3ded-546">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b3ded-546">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b3ded-547">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-547">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b3ded-548">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-548">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b3ded-549">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="b3ded-549">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b3ded-550">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="b3ded-550">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-551">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-551">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-552">W programie `ConfigureServices` wyróżnione wiersze zostały dodane przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-552">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-553">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-553">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-554">W programie upewnij `ConfigureServices` się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-554">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="b3ded-555">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-555">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b3ded-556">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="b3ded-556">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b3ded-557">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-557">Create the database</span></span>

<span data-ttu-id="b3ded-558">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="b3ded-558">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b3ded-559">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-559">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b3ded-560">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="b3ded-560">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b3ded-561">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="b3ded-561">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b3ded-562">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-562">Delete the database.</span></span> <span data-ttu-id="b3ded-563">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="b3ded-563">Any existing data is lost.</span></span>
* <span data-ttu-id="b3ded-564">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-564">Change the data model.</span></span> <span data-ttu-id="b3ded-565">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="b3ded-565">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b3ded-566">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-566">Run the app.</span></span>
* <span data-ttu-id="b3ded-567">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="b3ded-567">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b3ded-568">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-568">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b3ded-569">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="b3ded-569">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b3ded-570">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-570">When that is the case, use migrations.</span></span>

<span data-ttu-id="b3ded-571">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="b3ded-571">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b3ded-572">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-572">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b3ded-573">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="b3ded-573">Test the app</span></span>

* <span data-ttu-id="b3ded-574">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-574">Run the app.</span></span>
* <span data-ttu-id="b3ded-575">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-575">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b3ded-576">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="b3ded-576">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b3ded-577">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-577">Seed the database</span></span>

<span data-ttu-id="b3ded-578">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-578">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b3ded-579">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="b3ded-579">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b3ded-580">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-580">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b3ded-581">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-581">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b3ded-582">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-582">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b3ded-583">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="b3ded-583">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="b3ded-584">W *program.cs* Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="b3ded-584">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-585">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3ded-586">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="b3ded-586">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-587">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-587">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-588">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-588">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b3ded-589">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-589">Restart the app.</span></span>

* <span data-ttu-id="b3ded-590">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-590">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b3ded-591">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-591">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-592">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-592">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-593">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b3ded-593">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b3ded-594">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-594">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="b3ded-595">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="b3ded-595">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b3ded-596">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="b3ded-596">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b3ded-597">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-597">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b3ded-598">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-598">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-599">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-599">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b3ded-600">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="b3ded-600">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b3ded-601">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-601">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b3ded-602">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="b3ded-602">Asynchronous code</span></span>

<span data-ttu-id="b3ded-603">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="b3ded-603">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b3ded-604">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="b3ded-604">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b3ded-605">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="b3ded-605">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b3ded-606">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-606">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b3ded-607">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="b3ded-607">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b3ded-608">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="b3ded-608">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b3ded-609">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="b3ded-609">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b3ded-610">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="b3ded-610">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b3ded-611">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-611">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b3ded-612">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="b3ded-612">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b3ded-613">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="b3ded-613">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b3ded-614">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-614">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b3ded-615">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="b3ded-615">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="b3ded-616">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="b3ded-616">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b3ded-617">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-617">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b3ded-618">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-618">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b3ded-619">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-619">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b3ded-620">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="b3ded-620">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b3ded-621">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-621">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b3ded-622">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-622">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b3ded-623">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-623">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b3ded-624">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-624">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b3ded-625">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-625">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b3ded-626">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b3ded-626">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b3ded-627">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="b3ded-627">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b3ded-628">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="b3ded-628">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b3ded-629">Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji ASP.NET Core Razor stronach przy użyciu programu Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="b3ded-629">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="b3ded-630">Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="b3ded-630">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b3ded-631">Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-631">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b3ded-632">Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają sposób tworzenia przykładowej aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="b3ded-632">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="b3ded-633">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-633">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b3ded-634">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b3ded-634">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b3ded-635">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b3ded-635">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-636">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-636">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-637">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-637">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="b3ded-638">Znajomość [ Razor stron](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="b3ded-638">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="b3ded-639">Nowi programiści powinni zakończyć pracę [z Razor stronami](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="b3ded-639">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b3ded-640">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="b3ded-640">Troubleshooting</span></span>

<span data-ttu-id="b3ded-641">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b3ded-641">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b3ded-642">Dobrym sposobem uzyskania pomocy jest zaksięgowanie pytania [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) na [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="b3ded-642">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="b3ded-643">Aplikacja sieci Web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="b3ded-643">The Contoso University web app</span></span>

<span data-ttu-id="b3ded-644">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b3ded-644">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="b3ded-645">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-645">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b3ded-646">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="b3ded-646">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

<span data-ttu-id="b3ded-649">Styl interfejsu użytkownika tej witryny jest zbliżony do zawartości wygenerowanej przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="b3ded-649">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="b3ded-650">Fokus samouczka znajduje się na EF Core ze Razor stronami, a nie interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b3ded-650">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="b3ded-651">Tworzenie Razor aplikacji sieci Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="b3ded-651">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-652">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-652">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-653">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-653">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b3ded-654">Utwórz nową aplikację sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3ded-654">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="b3ded-655">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-655">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="b3ded-656">Ważne jest, aby nazwa projektu *ContosoUniversity* , tak aby przestrzenie nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-656">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="b3ded-657">Wybierz pozycję **ASP.NET Core 2,1** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-657">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="b3ded-658">Aby poznać obrazy powyższych kroków, zobacz [Tworzenie Razor aplikacji sieci Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="b3ded-658">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="b3ded-659">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-659">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-660">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-660">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b3ded-661">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="b3ded-661">Set up the site style</span></span>

<span data-ttu-id="b3ded-662">Kilka zmian powoduje skonfigurowanie menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="b3ded-662">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="b3ded-663">Aktualizowanie *stron/Shared/_Layout. cshtml* z następującymi zmianami:</span><span class="sxs-lookup"><span data-stu-id="b3ded-663">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="b3ded-664">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="b3ded-664">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b3ded-665">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-665">There are three occurrences.</span></span>

* <span data-ttu-id="b3ded-666">Dodaj pozycje menu dla **studentów**, **kursów**, **instruktorów** i **działów**, a następnie usuń wpis menu **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="b3ded-666">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="b3ded-667">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="b3ded-667">The changes are highlighted.</span></span> <span data-ttu-id="b3ded-668">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="b3ded-668">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="b3ded-669">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b3ded-669">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="b3ded-670">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-670">Create the data model</span></span>

<span data-ttu-id="b3ded-671">Utwórz klasy jednostek dla aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="b3ded-671">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="b3ded-672">Zacznij od następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="b3ded-672">Start with the following three entities:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="b3ded-674">Istnieje relacja jeden do wielu między elementami `Student` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-674">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="b3ded-675">Istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-675">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="b3ded-676">Student może zarejestrować się w dowolnej liczbie kursów.</span><span class="sxs-lookup"><span data-stu-id="b3ded-676">A student can enroll in any number of courses.</span></span> <span data-ttu-id="b3ded-677">Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.</span><span class="sxs-lookup"><span data-stu-id="b3ded-677">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="b3ded-678">W poniższych sekcjach zostanie utworzona Klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-678">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="b3ded-679">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="b3ded-679">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

<span data-ttu-id="b3ded-681">Utwórz folder *models* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-681">Create a *Models* folder.</span></span> <span data-ttu-id="b3ded-682">W folderze *modele* Utwórz plik klasy o nazwie *student.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-682">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="b3ded-683">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych (DB), która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-683">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="b3ded-684">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-684">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b3ded-685">W `classnameID` , `classname` jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-685">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="b3ded-686">Alternatywny automatycznie rozpoznany klucz podstawowy znajduje się `StudentID` w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-686">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="b3ded-687">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b3ded-687">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b3ded-688">Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="b3ded-688">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="b3ded-689">W tym przypadku `Enrollments` Właściwość a `Student entity` zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym `Student` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-689">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="b3ded-690">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="b3ded-690">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="b3ded-691">Powiązany `Enrollment` wiersz jest wierszem zawierającym wartość klucza podstawowego studenta w `StudentID` kolumnie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-691">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="b3ded-692">Załóżmy na przykład, że student o IDENTYFIKATORze 1 ma dwa wiersze w `Enrollment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-692">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="b3ded-693">`Enrollment`Tabela ma dwa wiersze z `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="b3ded-693">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="b3ded-694">`StudentID` jest kluczem obcym w `Enrollment` tabeli, który określa student w `Student` tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-694">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="b3ded-695">Jeśli właściwość nawigacji może zawierać wiele jednostek, właściwość nawigacji musi być typem listy, na przykład `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-695">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="b3ded-696">`ICollection<T>` można określić typ, taki jak `List<T>` lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-696">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b3ded-697">Gdy `ICollection<T>` jest używany, EF Core domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="b3ded-697">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="b3ded-698">Właściwości nawigacji, które przechowują wiele jednostek, pochodzą z relacji "wiele do wielu" i "jeden do wielu".</span><span class="sxs-lookup"><span data-stu-id="b3ded-698">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="b3ded-699">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="b3ded-699">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="b3ded-701">W folderze *modele* Utwórz *Enrollment.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-701">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="b3ded-702">`EnrollmentID`Właściwość jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="b3ded-702">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="b3ded-703">Ta jednostka używa `classnameID` wzorca zamiast `ID` `Student` obiektu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-703">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="b3ded-704">Zazwyczaj deweloperzy wybierają jeden wzorzec i używają go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-704">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="b3ded-705">W późniejszym samouczku, użycie identyfikatora bez ClassName jest pokazane, aby ułatwić implementację dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-705">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="b3ded-706">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-706">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b3ded-707">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null.</span><span class="sxs-lookup"><span data-stu-id="b3ded-707">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="b3ded-708">Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="b3ded-708">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b3ded-709">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-709">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b3ded-710">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="b3ded-710">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="b3ded-711">`Student`Jednostka różni się od `Student.Enrollments` właściwości nawigacji, która zawiera wiele `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-711">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="b3ded-712">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-712">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b3ded-713">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="b3ded-713">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b3ded-714">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-714">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b3ded-715">Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-715">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b3ded-716">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-716">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b3ded-717">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-717">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="b3ded-718">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="b3ded-718">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="b3ded-720">W folderze *modele* Utwórz *Course.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-720">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="b3ded-721">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-721">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b3ded-722">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-722">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b3ded-723">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast generowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-723">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="b3ded-724">Tworzenie szkieletu modelu ucznia</span><span class="sxs-lookup"><span data-stu-id="b3ded-724">Scaffold the student model</span></span>

<span data-ttu-id="b3ded-725">W tej sekcji model ucznia jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="b3ded-725">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="b3ded-726">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-726">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="b3ded-727">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="b3ded-727">Build the project.</span></span>
* <span data-ttu-id="b3ded-728">Utwórz folder *strony/uczniowie* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-728">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-729">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-729">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b3ded-730">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-730">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b3ded-731">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-731">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="b3ded-732">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="b3ded-732">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b3ded-733">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-733">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="b3ded-734">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę na **ContosoUniversity. models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-734">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="b3ded-735">Z listy rozwijanej **Klasa kontekstu danych** wybierz pozycję **ContosoUniversity. models. SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="b3ded-735">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="b3ded-736">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-736">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="b3ded-738">Zobacz Tworzenie [szkieletu modelu filmu w](xref:tutorials/razor-pages/model#scaffold-the-movie-model) przypadku problemu z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="b3ded-738">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-739">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-739">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b3ded-740">Uruchom następujące polecenia, aby uzyskać szkielet modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-740">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="b3ded-741">Proces szkieletu utworzył i zmienił następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="b3ded-741">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="b3ded-742">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="b3ded-742">Files created</span></span>

* <span data-ttu-id="b3ded-743">*Strony/uczniowie* Tworzenie, usuwanie, szczegóły, edytowanie, indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="b3ded-744">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="b3ded-744">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="b3ded-745">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="b3ded-745">File updates</span></span>

* <span data-ttu-id="b3ded-746">*Startup.cs* : szczegółowe zmiany w tym pliku opisano w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-746">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="b3ded-747">*appsettings.json* : Dodano parametry połączenia używane do nawiązania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-747">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b3ded-748">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="b3ded-748">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b3ded-749">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b3ded-749">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b3ded-750">Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-750">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b3ded-751">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="b3ded-751">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b3ded-752">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="b3ded-752">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b3ded-753">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="b3ded-753">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b3ded-754">Zapoznaj się z `ConfigureServices` metodą w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3ded-754">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="b3ded-755">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-755">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="b3ded-756">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="b3ded-756">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b3ded-757">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="b3ded-757">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="b3ded-758">Aktualizuj główne</span><span class="sxs-lookup"><span data-stu-id="b3ded-758">Update main</span></span>

<span data-ttu-id="b3ded-759">W *program.cs* Zmień metodę, `Main` Aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b3ded-759">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="b3ded-760">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="b3ded-760">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b3ded-761">Wywołaj  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="b3ded-761">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="b3ded-762">Usuń kontekst, gdy `EnsureCreated` Metoda zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="b3ded-762">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="b3ded-763">Poniższy kod przedstawia zaktualizowany plik *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-763">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="b3ded-764">`EnsureCreated` zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="b3ded-764">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="b3ded-765">Jeśli istnieje, nie zostanie podjęta żadna akcja.</span><span class="sxs-lookup"><span data-stu-id="b3ded-765">If it exists, no action is taken.</span></span> <span data-ttu-id="b3ded-766">Jeśli nie istnieje, zostanie utworzona baza danych i jej wszystkie schematy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-766">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="b3ded-767">`EnsureCreated` Program nie korzysta z migracji w celu utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-767">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="b3ded-768">Baza danych utworzona za pomocą programu `EnsureCreated` nie może zostać zaktualizowana później przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-768">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="b3ded-769">`EnsureCreated` jest wywoływana podczas uruchamiania aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="b3ded-769">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="b3ded-770">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-770">Delete the DB.</span></span>
* <span data-ttu-id="b3ded-771">Zmień schemat bazy danych (na przykład Dodaj `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="b3ded-771">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="b3ded-772">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="b3ded-772">Run the app.</span></span>
* <span data-ttu-id="b3ded-773">`EnsureCreated` tworzy bazę danych z `EmailAddress` kolumną.</span><span class="sxs-lookup"><span data-stu-id="b3ded-773">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="b3ded-774">`EnsureCreated` jest wygodnie wczesny podczas opracowywania, gdy schemat jest szybko zmieniany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-774">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="b3ded-775">W dalszej części tego samouczka baza danych została usunięta, a migracja jest używana.</span><span class="sxs-lookup"><span data-stu-id="b3ded-775">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b3ded-776">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="b3ded-776">Test the app</span></span>

<span data-ttu-id="b3ded-777">Uruchom aplikację i zaakceptuj cookie zasady.</span><span class="sxs-lookup"><span data-stu-id="b3ded-777">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="b3ded-778">Ta aplikacja nie przechowuje informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-778">This app doesn't keep personal information.</span></span> <span data-ttu-id="b3ded-779">Informacje o zasadach można znaleźć cookie w witrynie [pomocy technicznej ogólne rozporządzenie O ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="b3ded-779">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="b3ded-780">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-780">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b3ded-781">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="b3ded-781">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="b3ded-782">Sprawdzanie kontekstu bazy danych SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="b3ded-782">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="b3ded-783">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-783">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="b3ded-784">Kontekst danych pochodzi od elementu [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b3ded-784">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b3ded-785">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-785">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b3ded-786">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-786">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b3ded-787">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b3ded-787">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="b3ded-788">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="b3ded-788">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b3ded-789">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="b3ded-789">In EF Core terminology:</span></span>

* <span data-ttu-id="b3ded-790">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-790">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="b3ded-791">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-791">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b3ded-792">`DbSet<Enrollment>` i można `DbSet<Course>` je pominąć.</span><span class="sxs-lookup"><span data-stu-id="b3ded-792">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="b3ded-793">EF Core obejmuje te niejawnie `Student` , ponieważ jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="b3ded-793">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="b3ded-794">W tym samouczku należy zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-794">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="b3ded-795">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="b3ded-795">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b3ded-796">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="b3ded-796">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="b3ded-797">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="b3ded-797">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b3ded-798">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="b3ded-798">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b3ded-799">Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="b3ded-799">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="b3ded-800">Dodawanie kodu w celu zainicjowania bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="b3ded-800">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="b3ded-801">EF Core tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-801">EF Core creates an empty DB.</span></span> <span data-ttu-id="b3ded-802">W tej sekcji `Initialize` Metoda jest zapisywana w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="b3ded-802">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="b3ded-803">W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="b3ded-803">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="b3ded-804">Uwaga: Poprzedni kod używa `Models` dla przestrzeni nazw ( `namespace ContosoUniversity.Models` ), a nie `Data` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-804">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="b3ded-805">`Models` jest spójny z kodem generowanym przez program rusztowaer.</span><span class="sxs-lookup"><span data-stu-id="b3ded-805">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="b3ded-806">Aby uzyskać więcej informacji, zobacz [ten problem z szkieletem usługi GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="b3ded-806">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="b3ded-807">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-807">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="b3ded-808">Jeśli w bazie danych nie ma studentów, baza danych zostanie zainicjowana z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="b3ded-808">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="b3ded-809">Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="b3ded-809">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b3ded-810">`EnsureCreated`Metoda automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-810">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="b3ded-811">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-811">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="b3ded-812">W *program.cs* Zmień metodę, `Main` Aby wywołać `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="b3ded-812">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="b3ded-813">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3ded-813">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b3ded-814">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="b3ded-814">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b3ded-815">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b3ded-815">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b3ded-816">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="b3ded-816">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="b3ded-817">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3ded-817">View the DB</span></span>

<span data-ttu-id="b3ded-818">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="b3ded-818">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="b3ded-819">W rezultacie nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="b3ded-819">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="b3ded-820">Identyfikator GUID będzie różny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b3ded-820">The GUID will be different for each user.</span></span>
<span data-ttu-id="b3ded-821">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b3ded-821">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="b3ded-822">W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="b3ded-822">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="b3ded-823">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="b3ded-823">Expand the **Tables** node.</span></span>

<span data-ttu-id="b3ded-824">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="b3ded-824">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="b3ded-825">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="b3ded-825">Asynchronous code</span></span>

<span data-ttu-id="b3ded-826">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="b3ded-826">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b3ded-827">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="b3ded-827">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b3ded-828">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="b3ded-828">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b3ded-829">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="b3ded-829">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b3ded-830">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="b3ded-830">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b3ded-831">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="b3ded-831">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="b3ded-832">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="b3ded-832">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b3ded-833">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="b3ded-833">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b3ded-834">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-834">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="b3ded-835">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="b3ded-835">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b3ded-836">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="b3ded-836">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b3ded-837">Automatycznie Utwórz obiekt [zadania](/dotnet/api/system.threading.tasks.task) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="b3ded-837">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="b3ded-838">Aby uzyskać więcej informacji, zobacz [Typ zwracany zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="b3ded-838">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="b3ded-839">Niejawny typ zwracany `Task` reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="b3ded-839">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="b3ded-840">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="b3ded-840">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b3ded-841">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-841">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b3ded-842">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-842">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b3ded-843">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="b3ded-843">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b3ded-844">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="b3ded-844">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b3ded-845">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="b3ded-845">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="b3ded-846">Obejmuje, `ToListAsync` ,, `SingleOrDefaultAsync` `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-846">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b3ded-847">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="b3ded-847">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b3ded-848">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="b3ded-848">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b3ded-849">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="b3ded-849">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="b3ded-850">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b3ded-850">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="b3ded-851">W następnym samouczku są badane podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="b3ded-851">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="b3ded-852">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b3ded-852">Additional resources</span></span>

* [<span data-ttu-id="b3ded-853">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="b3ded-853">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="b3ded-854">Dalej</span><span class="sxs-lookup"><span data-stu-id="b3ded-854">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
