---
title: Razor Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8
author: rick-anderson
description: Pokazuje, jak utworzyć Razor aplikację stron przy użyciu Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
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
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424292"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="2770c-103">Razor Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8</span><span class="sxs-lookup"><span data-stu-id="2770c-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="2770c-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2770c-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2770c-105">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="2770c-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="2770c-106">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="2770c-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2770c-107">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="2770c-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2770c-108">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="2770c-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="2770c-109">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="2770c-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="2770c-110">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="2770c-111">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="2770c-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2770c-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="2770c-112">Prerequisites</span></span>

* <span data-ttu-id="2770c-113">Jeśli Razor dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do Razor stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="2770c-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="2770c-116">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-116">Database engines</span></span>

<span data-ttu-id="2770c-117">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="2770c-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="2770c-118">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="2770c-119">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="2770c-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2770c-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="2770c-120">Troubleshooting</span></span>

<span data-ttu-id="2770c-121">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="2770c-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="2770c-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="2770c-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="2770c-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="2770c-123">The sample app</span></span>

<span data-ttu-id="2770c-124">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2770c-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="2770c-125">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="2770c-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2770c-126">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="2770c-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="2770c-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="2770c-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="2770c-130">Fokus samouczka polega na tym, jak używać EF Core z ASP.NET Core, a nie sposobu dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2770c-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="2770c-131">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="2770c-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-133">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="2770c-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2770c-134">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="2770c-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2770c-135">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="2770c-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="2770c-136">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="2770c-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="2770c-137">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 5,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="2770c-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-139">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="2770c-140">Uruchom następujące polecenia, aby utworzyć Razor Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="2770c-140">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="2770c-141">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="2770c-141">Set up the site style</span></span>

<span data-ttu-id="2770c-142">Skopiuj i wklej następujący kod do pliku *Pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="2770c-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="2770c-143">Plik układu ustawia nagłówek, stopkę i menu witryny.</span><span class="sxs-lookup"><span data-stu-id="2770c-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="2770c-144">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="2770c-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2770c-145">Każde wystąpienie "ContosoUniversity" na "University-Uniwersytet".</span><span class="sxs-lookup"><span data-stu-id="2770c-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="2770c-146">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="2770c-146">There are three occurrences.</span></span>
* <span data-ttu-id="2770c-147">Wpisy menu **Home** i **privacy** są usuwane.</span><span class="sxs-lookup"><span data-stu-id="2770c-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="2770c-148">Wpisy są **dodawane do programu**, **studentów**, **kursów**, **instruktorów**i **działów**.</span><span class="sxs-lookup"><span data-stu-id="2770c-148">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="2770c-149">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="2770c-149">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="2770c-150">Poprzedni kod zastępuje tekst dotyczący ASP.NET Core tekstem dla tej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="2770c-151">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="2770c-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="2770c-152">Model danych</span><span class="sxs-lookup"><span data-stu-id="2770c-152">The data model</span></span>

<span data-ttu-id="2770c-153">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="2770c-153">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="2770c-155">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="2770c-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="2770c-156">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="2770c-156">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="2770c-158">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="2770c-159">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="2770c-160">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="2770c-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="2770c-161">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="2770c-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="2770c-162">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="2770c-163">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="2770c-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="2770c-164">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="2770c-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2770c-165">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="2770c-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="2770c-166">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="2770c-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="2770c-167">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="2770c-168">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="2770c-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="2770c-169">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="2770c-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="2770c-170">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="2770c-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="2770c-171">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="2770c-172">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="2770c-173">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="2770c-174">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="2770c-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="2770c-175">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="2770c-175">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="2770c-177">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="2770c-178">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="2770c-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="2770c-179">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="2770c-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="2770c-180">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="2770c-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="2770c-181">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="2770c-182">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="2770c-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2770c-183">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="2770c-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="2770c-184">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="2770c-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2770c-185">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="2770c-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2770c-186">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="2770c-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="2770c-187">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="2770c-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2770c-188">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="2770c-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2770c-189">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="2770c-190">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="2770c-191">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="2770c-192">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="2770c-193">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="2770c-193">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="2770c-195">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="2770c-196">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2770c-197">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2770c-198">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="2770c-199">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="2770c-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="2770c-200">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="2770c-200">Scaffold Student pages</span></span>

<span data-ttu-id="2770c-201">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="2770c-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="2770c-202">Klasa EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="2770c-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="2770c-203">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="2770c-204">Pochodzi od <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="2770c-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="2770c-205">Razor strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="2770c-205">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-207">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="2770c-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="2770c-208">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="2770c-208">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2770c-209">W oknie dialogowym **Dodaj nowy element szkieletowy** :</span><span class="sxs-lookup"><span data-stu-id="2770c-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="2770c-210">Na lewej karcie Wybierz pozycję **zainstalowane > typowe Razor strony >**</span><span class="sxs-lookup"><span data-stu-id="2770c-210">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="2770c-211">Wybierz \*\* Razor strony przy użyciu Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2770c-211">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="2770c-212">Na **stronie Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="2770c-212">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="2770c-213">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="2770c-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="2770c-214">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="2770c-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="2770c-215">Zmień nazwę kontekstu danych na End `SchoolContext` zamiast `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="2770c-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="2770c-216">Nazwa zaktualizowanego kontekstu: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="2770c-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="2770c-217">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2770c-217">Select **Add**.</span></span>

<span data-ttu-id="2770c-218">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="2770c-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-220">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="2770c-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="2770c-221">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="2770c-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="2770c-222">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2770c-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="2770c-223">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="2770c-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="2770c-224">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="2770c-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="2770c-225">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="2770c-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="2770c-226">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="2770c-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="2770c-227">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="2770c-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="2770c-228">Jeśli poprzedni krok zakończy się niepowodzeniem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="2770c-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="2770c-229">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2770c-229">The scaffolding process:</span></span>

* <span data-ttu-id="2770c-230">Tworzy Razor strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="2770c-230">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="2770c-231">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-232">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-233">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-234">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-235">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="2770c-236">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="2770c-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="2770c-237">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2770c-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="2770c-238">Dodaje parametry połączenia z bazą danych do *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="2770c-238">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="2770c-239">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="2770c-239">Database connection string</span></span>

<span data-ttu-id="2770c-240">Narzędzie do tworzenia szkieletu generuje parametry połączenia w *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="2770c-240">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2770c-242">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="2770c-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="2770c-243">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="2770c-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="2770c-244">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="2770c-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2770c-246">Skróć parametry połączenia oprogramowania SQLite do *bazy danych cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="2770c-246">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="2770c-247">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-247">Update the database context class</span></span>

<span data-ttu-id="2770c-248">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="2770c-249">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2770c-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2770c-250">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="2770c-251">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="2770c-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2770c-252">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="2770c-253">Poprzedni kod zmienia się z pojedynczej `DbSet<Student> Student` na plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="2770c-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="2770c-254">Aby Razor kod stron był zgodny z nową `DBSet` nazwą, wprowadź globalną zmianę z: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="2770c-254">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="2770c-255">do `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="2770c-255">to: `_context.Students.`</span></span>

<span data-ttu-id="2770c-256">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="2770c-256">There are 8 occurrences.</span></span>

<span data-ttu-id="2770c-257">Ponieważ zestaw jednostek zawiera wiele jednostek, wielu deweloperów preferuje `DBSet` nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="2770c-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="2770c-258">Wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="2770c-258">The highlighted code:</span></span>

* <span data-ttu-id="2770c-259">Tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="2770c-260">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="2770c-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="2770c-261">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="2770c-262">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="2770c-263">Wywołania <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="2770c-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="2770c-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="2770c-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="2770c-265">Jest wywoływana, gdy została `SchoolContext` zainicjowana, ale zanim model zostanie zablokowany i użyty do zainicjowania kontekstu.</span><span class="sxs-lookup"><span data-stu-id="2770c-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="2770c-266">Jest wymagany, ponieważ w dalszej części samouczka `Student` jednostka będzie miała odwołania do innych jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="2770c-267">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="2770c-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="2770c-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2770c-268">Startup.cs</span></span>

<span data-ttu-id="2770c-269">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2770c-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2770c-270">Usługi takie jak `SchoolContext` są rejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="2770c-271">Składniki, które wymagają tych usług, takie jak Razor strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="2770c-271">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="2770c-272">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="2770c-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2770c-273">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2770c-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2770c-275">Program szkieleter dodał następujące wyróżnione wiersze:</span><span class="sxs-lookup"><span data-stu-id="2770c-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2770c-277">Sprawdź kod dodany przez wywołania szkieletu `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="2770c-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2770c-278">Aby uzyskać informacje na temat korzystania z produkcyjnej bazy danych, zobacz temat [Korzystanie z oprogramowania SQLite na potrzeby programowania. SQL Server do produkcji](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="2770c-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="2770c-279">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="2770c-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2770c-280">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="2770c-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="2770c-281">Dodaj filtr wyjątku bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-281">Add the database exception filter</span></span>

<span data-ttu-id="2770c-282">Dodaj `AddDatabaseDeveloperPageExceptionFilter` do `ConfigureServices` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="2770c-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="2770c-284">Dodaj pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="2770c-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="2770c-285">W obszarze PMC wprowadź następujące polecenie, aby dodać pakiet NuGet:</span><span class="sxs-lookup"><span data-stu-id="2770c-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="2770c-287">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`Pakiet NuGet zawiera ASP.NET Core oprogramowanie pośredniczące dla Entity Framework Core stron błędów.</span><span class="sxs-lookup"><span data-stu-id="2770c-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="2770c-288">To oprogramowanie pośredniczące pomaga wykrywać i diagnozować błędy za pomocą migracji Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="2770c-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="2770c-289">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-289">Create the database</span></span>

<span data-ttu-id="2770c-290">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="2770c-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="2770c-291">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="2770c-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="2770c-292">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="2770c-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="2770c-293">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="2770c-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="2770c-294">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-294">Delete the database.</span></span> <span data-ttu-id="2770c-295">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="2770c-295">Any existing data is lost.</span></span>
* <span data-ttu-id="2770c-296">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-296">Change the data model.</span></span> <span data-ttu-id="2770c-297">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="2770c-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="2770c-298">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-298">Run the app.</span></span>
* <span data-ttu-id="2770c-299">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="2770c-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="2770c-300">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="2770c-301">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="2770c-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="2770c-302">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="2770c-303">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="2770c-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="2770c-304">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="2770c-305">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2770c-305">Test the app</span></span>

* <span data-ttu-id="2770c-306">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-306">Run the app.</span></span>
* <span data-ttu-id="2770c-307">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="2770c-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="2770c-308">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="2770c-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="2770c-309">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-309">Seed the database</span></span>

<span data-ttu-id="2770c-310">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="2770c-311">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="2770c-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="2770c-312">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="2770c-313">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="2770c-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="2770c-314">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="2770c-315">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="2770c-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="2770c-316">W *program.cs*Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="2770c-316">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2770c-318">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="2770c-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="2770c-319">Odpowiedz przy użyciu `Y` , aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-321">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="2770c-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="2770c-322">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-322">Restart the app.</span></span>
* <span data-ttu-id="2770c-323">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="2770c-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="2770c-324">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-326">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2770c-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="2770c-327">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="2770c-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="2770c-328">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="2770c-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="2770c-329">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="2770c-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="2770c-330">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="2770c-331">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2770c-333">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="2770c-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="2770c-334">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="2770c-335">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="2770c-335">Asynchronous code</span></span>

<span data-ttu-id="2770c-336">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="2770c-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2770c-337">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="2770c-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2770c-338">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="2770c-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2770c-339">W przypadku kodu synchronicznego wiele wątków może zostać rozwiązanych, chociaż nie działają, ponieważ oczekują na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="2770c-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2770c-340">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="2770c-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2770c-341">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="2770c-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="2770c-342">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="2770c-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="2770c-343">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="2770c-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2770c-344">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="2770c-345">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="2770c-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="2770c-346">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="2770c-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="2770c-347">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="2770c-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="2770c-348">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="2770c-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="2770c-349">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="2770c-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2770c-350">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2770c-351">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2770c-352">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2770c-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2770c-353">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="2770c-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="2770c-354">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="2770c-355">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="2770c-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2770c-356">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="2770c-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2770c-357">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="2770c-358">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="2770c-359">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="2770c-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="2770c-360">Zagadnienia dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="2770c-360">Performance considerations</span></span>

<span data-ttu-id="2770c-361">Ogólnie rzecz biorąc, Strona sieci Web nie powinna ładować dowolnej liczby wierszy.</span><span class="sxs-lookup"><span data-stu-id="2770c-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="2770c-362">Zapytanie powinno używać podejścia do ograniczania.</span><span class="sxs-lookup"><span data-stu-id="2770c-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="2770c-363">Na przykład poprzednie zapytanie może użyć, `Take` Aby ograniczyć liczbę zwracanych wierszy:</span><span class="sxs-lookup"><span data-stu-id="2770c-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2770c-364">Wyliczenie dużej tabeli w widoku może zwrócić częściowo skonstruowaną odpowiedź HTTP 200, jeśli wyjątek bazy danych wystąpił częściowo przez wyliczenie.</span><span class="sxs-lookup"><span data-stu-id="2770c-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="2770c-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> wartość domyślna to 1024.</span><span class="sxs-lookup"><span data-stu-id="2770c-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="2770c-366">Następujące zestawy kodu `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="2770c-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2770c-367">Stronicowanie jest omówione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="2770c-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2770c-368">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="2770c-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2770c-369">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="2770c-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2770c-370">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="2770c-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="2770c-371">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="2770c-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2770c-372">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="2770c-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2770c-373">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="2770c-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="2770c-374">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="2770c-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="2770c-375">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="2770c-376">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="2770c-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2770c-377">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="2770c-377">Prerequisites</span></span>

* <span data-ttu-id="2770c-378">Jeśli Razor dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do Razor stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="2770c-378">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="2770c-381">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-381">Database engines</span></span>

<span data-ttu-id="2770c-382">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="2770c-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="2770c-383">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="2770c-384">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="2770c-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2770c-385">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="2770c-385">Troubleshooting</span></span>

<span data-ttu-id="2770c-386">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="2770c-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="2770c-387">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="2770c-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="2770c-388">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="2770c-388">The sample app</span></span>

<span data-ttu-id="2770c-389">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2770c-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="2770c-390">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="2770c-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2770c-391">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="2770c-391">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="2770c-394">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="2770c-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="2770c-395">Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2770c-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="2770c-396">Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="2770c-397">Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="2770c-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="2770c-398">Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="2770c-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2770c-400">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="2770c-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="2770c-401">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="2770c-401">Build the project.</span></span>
* <span data-ttu-id="2770c-402">W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2770c-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="2770c-403">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2770c-405">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="2770c-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="2770c-406">Usuń *ContosoUniversity. csproj*i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="2770c-406">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="2770c-407">W *program.cs*, komentarz `#define Startup` `StartupSQLite` jest używany.</span><span class="sxs-lookup"><span data-stu-id="2770c-407">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="2770c-408">Usuń *appSettings.js*i zmień nazwę *appSettingsSQLite.jsna* na *appSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="2770c-408">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="2770c-409">Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.</span><span class="sxs-lookup"><span data-stu-id="2770c-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="2770c-410">Wykonaj wyszukiwanie globalne dla `#if SQLiteVersion` i Usuń `#if SQLiteVersion` oraz skojarzoną `#endif` instrukcję.</span><span class="sxs-lookup"><span data-stu-id="2770c-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="2770c-411">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="2770c-411">Build the project.</span></span>
* <span data-ttu-id="2770c-412">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2770c-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="2770c-413">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="2770c-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="2770c-414">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="2770c-415">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="2770c-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-417">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="2770c-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2770c-418">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="2770c-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2770c-419">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="2770c-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="2770c-420">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="2770c-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="2770c-421">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="2770c-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-423">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="2770c-424">Uruchom następujące polecenia, aby utworzyć Razor Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="2770c-424">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="2770c-425">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="2770c-425">Set up the site style</span></span>

<span data-ttu-id="2770c-426">Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2770c-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="2770c-427">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="2770c-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="2770c-428">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="2770c-428">There are three occurrences.</span></span>

* <span data-ttu-id="2770c-429">Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów**, **kursów**, **instruktorów**i **działów**.</span><span class="sxs-lookup"><span data-stu-id="2770c-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="2770c-430">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="2770c-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="2770c-431">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2770c-431">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="2770c-432">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="2770c-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="2770c-433">Model danych</span><span class="sxs-lookup"><span data-stu-id="2770c-433">The data model</span></span>

<span data-ttu-id="2770c-434">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="2770c-434">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="2770c-436">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="2770c-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="2770c-437">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="2770c-437">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="2770c-439">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="2770c-440">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="2770c-441">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="2770c-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="2770c-442">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="2770c-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="2770c-443">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="2770c-444">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="2770c-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="2770c-445">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="2770c-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2770c-446">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="2770c-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="2770c-447">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="2770c-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="2770c-448">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="2770c-449">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="2770c-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="2770c-450">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="2770c-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="2770c-451">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="2770c-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="2770c-452">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="2770c-453">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="2770c-454">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="2770c-455">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="2770c-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="2770c-456">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="2770c-456">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="2770c-458">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="2770c-459">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="2770c-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="2770c-460">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="2770c-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="2770c-461">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="2770c-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="2770c-462">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="2770c-463">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="2770c-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2770c-464">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="2770c-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="2770c-465">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="2770c-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2770c-466">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="2770c-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2770c-467">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="2770c-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="2770c-468">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="2770c-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2770c-469">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="2770c-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2770c-470">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="2770c-471">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="2770c-472">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="2770c-473">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="2770c-474">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="2770c-474">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="2770c-476">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="2770c-477">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2770c-478">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2770c-479">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="2770c-480">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="2770c-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="2770c-481">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="2770c-481">Scaffold Student pages</span></span>

<span data-ttu-id="2770c-482">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="2770c-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="2770c-483">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="2770c-483">An EF Core *context* class.</span></span> <span data-ttu-id="2770c-484">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="2770c-485">Pochodzi od `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="2770c-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="2770c-486">Razor strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="2770c-486">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-488">Utwórz folder *uczniów* w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="2770c-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="2770c-489">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="2770c-489">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2770c-490">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2770c-490">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="2770c-491">Na **stronie Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="2770c-491">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="2770c-492">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="2770c-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="2770c-493">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="2770c-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="2770c-494">Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="2770c-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="2770c-495">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2770c-495">Select **Add**.</span></span>

<span data-ttu-id="2770c-496">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="2770c-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-498">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="2770c-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="2770c-499">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="2770c-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="2770c-500">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2770c-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="2770c-501">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="2770c-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="2770c-502">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="2770c-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="2770c-503">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="2770c-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="2770c-504">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="2770c-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="2770c-505">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="2770c-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="2770c-506">Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="2770c-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="2770c-507">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2770c-507">The scaffolding process:</span></span>

* <span data-ttu-id="2770c-508">Tworzy Razor strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="2770c-508">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="2770c-509">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-510">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-511">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-512">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="2770c-513">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="2770c-514">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="2770c-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="2770c-515">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2770c-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="2770c-516">Dodaje parametry połączenia z bazą danych do *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="2770c-516">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="2770c-517">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="2770c-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2770c-519">*appsettings.jsw* pliku określa parametry połączenia [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="2770c-519">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="2770c-520">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="2770c-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="2770c-521">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="2770c-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2770c-523">Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="2770c-523">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="2770c-524">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-524">Update the database context class</span></span>

<span data-ttu-id="2770c-525">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="2770c-526">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2770c-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2770c-527">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="2770c-528">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="2770c-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2770c-529">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="2770c-530">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="2770c-531">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="2770c-531">In EF Core terminology:</span></span>

* <span data-ttu-id="2770c-532">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="2770c-533">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2770c-534">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="2770c-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="2770c-535">Ponieważ narzędzie tworzenia szkieletów utworzyło `Student` nieogólnymi, ten krok zmienia go na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="2770c-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="2770c-536">Aby Razor kod stron był zgodny z nową nazwą nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="2770c-536">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="2770c-537">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="2770c-537">There are 8 occurrences.</span></span>

<span data-ttu-id="2770c-538">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="2770c-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="2770c-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2770c-539">Startup.cs</span></span>

<span data-ttu-id="2770c-540">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2770c-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2770c-541">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2770c-542">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="2770c-542">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2770c-543">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="2770c-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2770c-544">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2770c-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-546">W programie `ConfigureServices` wyróżnione wiersze zostały dodane przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2770c-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-548">W programie upewnij `ConfigureServices` się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="2770c-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="2770c-549">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="2770c-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2770c-550">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="2770c-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="2770c-551">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-551">Create the database</span></span>

<span data-ttu-id="2770c-552">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="2770c-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="2770c-553">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="2770c-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="2770c-554">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="2770c-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="2770c-555">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="2770c-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="2770c-556">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-556">Delete the database.</span></span> <span data-ttu-id="2770c-557">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="2770c-557">Any existing data is lost.</span></span>
* <span data-ttu-id="2770c-558">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-558">Change the data model.</span></span> <span data-ttu-id="2770c-559">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="2770c-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="2770c-560">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-560">Run the app.</span></span>
* <span data-ttu-id="2770c-561">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="2770c-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="2770c-562">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="2770c-563">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="2770c-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="2770c-564">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="2770c-565">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="2770c-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="2770c-566">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="2770c-567">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2770c-567">Test the app</span></span>

* <span data-ttu-id="2770c-568">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-568">Run the app.</span></span>
* <span data-ttu-id="2770c-569">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="2770c-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="2770c-570">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="2770c-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="2770c-571">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-571">Seed the database</span></span>

<span data-ttu-id="2770c-572">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="2770c-573">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="2770c-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="2770c-574">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="2770c-575">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="2770c-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="2770c-576">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="2770c-577">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="2770c-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="2770c-578">W *program.cs*Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="2770c-578">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2770c-580">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="2770c-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-582">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="2770c-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="2770c-583">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-583">Restart the app.</span></span>

* <span data-ttu-id="2770c-584">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="2770c-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="2770c-585">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-587">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2770c-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="2770c-588">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="2770c-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="2770c-589">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="2770c-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="2770c-590">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="2770c-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="2770c-591">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="2770c-592">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2770c-594">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="2770c-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="2770c-595">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="2770c-596">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="2770c-596">Asynchronous code</span></span>

<span data-ttu-id="2770c-597">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="2770c-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2770c-598">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="2770c-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2770c-599">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="2770c-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2770c-600">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="2770c-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2770c-601">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="2770c-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2770c-602">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="2770c-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="2770c-603">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="2770c-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="2770c-604">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="2770c-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2770c-605">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="2770c-606">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="2770c-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="2770c-607">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="2770c-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="2770c-608">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="2770c-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="2770c-609">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="2770c-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="2770c-610">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="2770c-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2770c-611">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2770c-612">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2770c-613">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2770c-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2770c-614">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="2770c-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="2770c-615">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="2770c-616">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="2770c-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2770c-617">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="2770c-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2770c-618">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="2770c-619">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="2770c-620">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="2770c-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2770c-621">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="2770c-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2770c-622">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="2770c-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2770c-623">Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji ASP.NET Core Razor stronach przy użyciu programu Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="2770c-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="2770c-624">Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="2770c-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2770c-625">Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="2770c-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2770c-626">Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają sposób tworzenia przykładowej aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="2770c-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="2770c-627">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="2770c-628">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="2770c-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2770c-629">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="2770c-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="2770c-632">Znajomość [ Razor stron](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="2770c-632">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="2770c-633">Nowi programiści powinni zakończyć pracę [z Razor stronami](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="2770c-633">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2770c-634">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="2770c-634">Troubleshooting</span></span>

<span data-ttu-id="2770c-635">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="2770c-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="2770c-636">Dobrym sposobem uzyskania pomocy jest zaksięgowanie pytania [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) na [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="2770c-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="2770c-637">Aplikacja sieci Web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="2770c-637">The Contoso University web app</span></span>

<span data-ttu-id="2770c-638">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2770c-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="2770c-639">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="2770c-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2770c-640">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="2770c-640">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

<span data-ttu-id="2770c-643">Styl interfejsu użytkownika tej witryny jest zbliżony do zawartości wygenerowanej przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="2770c-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="2770c-644">Fokus samouczka znajduje się na EF Core ze Razor stronami, a nie interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2770c-644">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="2770c-645">Tworzenie Razor aplikacji sieci Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="2770c-645">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-647">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="2770c-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2770c-648">Utwórz nową aplikację sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2770c-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="2770c-649">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="2770c-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="2770c-650">Ważne jest, aby nazwa projektu *ContosoUniversity* , tak aby przestrzenie nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="2770c-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="2770c-651">Wybierz pozycję **ASP.NET Core 2,1** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="2770c-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="2770c-652">Aby poznać obrazy powyższych kroków, zobacz [Tworzenie Razor aplikacji sieci Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="2770c-652">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="2770c-653">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="2770c-655">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="2770c-655">Set up the site style</span></span>

<span data-ttu-id="2770c-656">Kilka zmian powoduje skonfigurowanie menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="2770c-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="2770c-657">Aktualizowanie *stron/Shared/_Layout. cshtml* z następującymi zmianami:</span><span class="sxs-lookup"><span data-stu-id="2770c-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="2770c-658">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="2770c-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="2770c-659">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="2770c-659">There are three occurrences.</span></span>

* <span data-ttu-id="2770c-660">Dodaj pozycje menu dla **studentów**, **kursów**, **instruktorów**i **działów**, a następnie usuń wpis menu **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="2770c-660">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="2770c-661">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="2770c-661">The changes are highlighted.</span></span> <span data-ttu-id="2770c-662">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="2770c-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="2770c-663">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2770c-663">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="2770c-664">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="2770c-664">Create the data model</span></span>

<span data-ttu-id="2770c-665">Utwórz klasy jednostek dla aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="2770c-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="2770c-666">Zacznij od następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="2770c-666">Start with the following three entities:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="2770c-668">Istnieje relacja jeden do wielu między elementami `Student` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="2770c-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="2770c-669">Istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="2770c-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="2770c-670">Student może zarejestrować się w dowolnej liczbie kursów.</span><span class="sxs-lookup"><span data-stu-id="2770c-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="2770c-671">Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.</span><span class="sxs-lookup"><span data-stu-id="2770c-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="2770c-672">W poniższych sekcjach zostanie utworzona Klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="2770c-673">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="2770c-673">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

<span data-ttu-id="2770c-675">Utwórz folder *models* .</span><span class="sxs-lookup"><span data-stu-id="2770c-675">Create a *Models* folder.</span></span> <span data-ttu-id="2770c-676">W folderze *modele* Utwórz plik klasy o nazwie *student.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="2770c-677">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych (DB), która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="2770c-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="2770c-678">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="2770c-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="2770c-679">W `classnameID` , `classname` jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="2770c-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="2770c-680">Alternatywny automatycznie rozpoznany klucz podstawowy znajduje się `StudentID` w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="2770c-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="2770c-681">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="2770c-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2770c-682">Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="2770c-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="2770c-683">W tym przypadku `Enrollments` Właściwość a `Student entity` zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym `Student` .</span><span class="sxs-lookup"><span data-stu-id="2770c-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="2770c-684">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="2770c-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="2770c-685">Powiązany `Enrollment` wiersz jest wierszem zawierającym wartość klucza podstawowego studenta w `StudentID` kolumnie.</span><span class="sxs-lookup"><span data-stu-id="2770c-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="2770c-686">Załóżmy na przykład, że student o IDENTYFIKATORze 1 ma dwa wiersze w `Enrollment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="2770c-687">`Enrollment`Tabela ma dwa wiersze z `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="2770c-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="2770c-688">`StudentID` jest kluczem obcym w `Enrollment` tabeli, który określa student w `Student` tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="2770c-689">Jeśli właściwość nawigacji może zawierać wiele jednostek, właściwość nawigacji musi być typem listy, na przykład `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="2770c-690">`ICollection<T>` można określić typ, taki jak `List<T>` lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="2770c-691">Gdy `ICollection<T>` jest używany, EF Core domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="2770c-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="2770c-692">Właściwości nawigacji, które przechowują wiele jednostek, pochodzą z relacji "wiele do wielu" i "jeden do wielu".</span><span class="sxs-lookup"><span data-stu-id="2770c-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="2770c-693">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="2770c-693">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="2770c-695">W folderze *modele* Utwórz *Enrollment.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="2770c-696">`EnrollmentID`Właściwość jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="2770c-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="2770c-697">Ta jednostka używa `classnameID` wzorca zamiast `ID` `Student` obiektu.</span><span class="sxs-lookup"><span data-stu-id="2770c-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="2770c-698">Zazwyczaj deweloperzy wybierają jeden wzorzec i używają go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="2770c-699">W późniejszym samouczku, użycie identyfikatora bez ClassName jest pokazane, aby ułatwić implementację dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="2770c-700">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="2770c-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2770c-701">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null.</span><span class="sxs-lookup"><span data-stu-id="2770c-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="2770c-702">Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="2770c-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2770c-703">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="2770c-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2770c-704">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="2770c-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="2770c-705">`Student`Jednostka różni się od `Student.Enrollments` właściwości nawigacji, która zawiera wiele `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="2770c-706">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="2770c-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2770c-707">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="2770c-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2770c-708">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="2770c-709">Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="2770c-710">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="2770c-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="2770c-711">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="2770c-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="2770c-712">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="2770c-712">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="2770c-714">W folderze *modele* Utwórz *Course.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="2770c-715">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2770c-716">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2770c-717">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast generowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="2770c-718">Tworzenie szkieletu modelu ucznia</span><span class="sxs-lookup"><span data-stu-id="2770c-718">Scaffold the student model</span></span>

<span data-ttu-id="2770c-719">W tej sekcji model ucznia jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="2770c-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="2770c-720">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="2770c-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="2770c-721">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="2770c-721">Build the project.</span></span>
* <span data-ttu-id="2770c-722">Utwórz folder *strony/uczniowie* .</span><span class="sxs-lookup"><span data-stu-id="2770c-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2770c-724">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="2770c-724">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2770c-725">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2770c-725">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="2770c-726">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2770c-726">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2770c-727">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="2770c-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="2770c-728">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę na **ContosoUniversity. models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="2770c-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="2770c-729">Z listy rozwijanej **Klasa kontekstu danych** wybierz pozycję **ContosoUniversity. models. SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="2770c-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="2770c-730">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2770c-730">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="2770c-732">Zobacz Tworzenie [szkieletu modelu filmu w](xref:tutorials/razor-pages/model#scaffold-the-movie-model) przypadku problemu z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="2770c-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2770c-734">Uruchom następujące polecenia, aby uzyskać szkielet modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="2770c-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="2770c-735">Proces szkieletu utworzył i zmienił następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2770c-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2770c-736">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="2770c-736">Files created</span></span>

* <span data-ttu-id="2770c-737">*Strony/uczniowie* Tworzenie, usuwanie, szczegóły, edytowanie, indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="2770c-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="2770c-738">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="2770c-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="2770c-739">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="2770c-739">File updates</span></span>

* <span data-ttu-id="2770c-740">*Startup.cs* : szczegółowe zmiany w tym pliku opisano w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2770c-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="2770c-741">*appsettings.json* : dodano parametry połączenia używane do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-741">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2770c-742">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="2770c-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2770c-743">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2770c-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2770c-744">Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2770c-745">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="2770c-745">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2770c-746">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="2770c-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2770c-747">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2770c-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2770c-748">Zapoznaj się z `ConfigureServices` metodą w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2770c-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="2770c-749">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2770c-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="2770c-750">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="2770c-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2770c-751">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="2770c-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="2770c-752">Aktualizuj główne</span><span class="sxs-lookup"><span data-stu-id="2770c-752">Update main</span></span>

<span data-ttu-id="2770c-753">W *program.cs*Zmień metodę, `Main` Aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="2770c-753">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="2770c-754">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2770c-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="2770c-755">Wywołaj  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="2770c-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="2770c-756">Usuń kontekst, gdy `EnsureCreated` Metoda zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="2770c-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="2770c-757">Poniższy kod przedstawia zaktualizowany plik *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="2770c-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="2770c-758">`EnsureCreated` zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="2770c-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="2770c-759">Jeśli istnieje, nie zostanie podjęta żadna akcja.</span><span class="sxs-lookup"><span data-stu-id="2770c-759">If it exists, no action is taken.</span></span> <span data-ttu-id="2770c-760">Jeśli nie istnieje, zostanie utworzona baza danych i jej wszystkie schematy.</span><span class="sxs-lookup"><span data-stu-id="2770c-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="2770c-761">`EnsureCreated` Program nie korzysta z migracji w celu utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="2770c-762">Baza danych utworzona za pomocą programu `EnsureCreated` nie może zostać zaktualizowana później przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="2770c-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="2770c-763">`EnsureCreated` jest wywoływana podczas uruchamiania aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="2770c-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="2770c-764">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-764">Delete the DB.</span></span>
* <span data-ttu-id="2770c-765">Zmień schemat bazy danych (na przykład Dodaj `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="2770c-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="2770c-766">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="2770c-766">Run the app.</span></span>
* <span data-ttu-id="2770c-767">`EnsureCreated` tworzy bazę danych z `EmailAddress` kolumną.</span><span class="sxs-lookup"><span data-stu-id="2770c-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="2770c-768">`EnsureCreated` jest wygodnie wczesny podczas opracowywania, gdy schemat jest szybko zmieniany.</span><span class="sxs-lookup"><span data-stu-id="2770c-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="2770c-769">W dalszej części tego samouczka baza danych została usunięta, a migracja jest używana.</span><span class="sxs-lookup"><span data-stu-id="2770c-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="2770c-770">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2770c-770">Test the app</span></span>

<span data-ttu-id="2770c-771">Uruchom aplikację i zaakceptuj cookie zasady.</span><span class="sxs-lookup"><span data-stu-id="2770c-771">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="2770c-772">Ta aplikacja nie przechowuje informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="2770c-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="2770c-773">Informacje o zasadach można znaleźć cookie w witrynie [pomocy technicznej ogólne rozporządzenie O ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2770c-773">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="2770c-774">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="2770c-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="2770c-775">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="2770c-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="2770c-776">Sprawdzanie kontekstu bazy danych SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="2770c-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="2770c-777">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="2770c-778">Kontekst danych pochodzi od elementu [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2770c-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2770c-779">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="2770c-780">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="2770c-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2770c-781">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="2770c-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="2770c-782">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="2770c-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="2770c-783">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="2770c-783">In EF Core terminology:</span></span>

* <span data-ttu-id="2770c-784">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="2770c-785">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2770c-786">`DbSet<Enrollment>` i można `DbSet<Course>` je pominąć.</span><span class="sxs-lookup"><span data-stu-id="2770c-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="2770c-787">EF Core obejmuje te niejawnie `Student` , ponieważ jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="2770c-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="2770c-788">W tym samouczku należy zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="2770c-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="2770c-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="2770c-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="2770c-790">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="2770c-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="2770c-791">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="2770c-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="2770c-792">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="2770c-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="2770c-793">Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="2770c-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="2770c-794">Dodawanie kodu w celu zainicjowania bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="2770c-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="2770c-795">EF Core tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="2770c-796">W tej sekcji `Initialize` Metoda jest zapisywana w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="2770c-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="2770c-797">W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="2770c-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="2770c-798">Uwaga: Poprzedni kod używa `Models` dla przestrzeni nazw ( `namespace ContosoUniversity.Models` ), a nie `Data` .</span><span class="sxs-lookup"><span data-stu-id="2770c-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="2770c-799">`Models` jest spójny z kodem generowanym przez program rusztowaer.</span><span class="sxs-lookup"><span data-stu-id="2770c-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="2770c-800">Aby uzyskać więcej informacji, zobacz [ten problem z szkieletem usługi GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="2770c-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="2770c-801">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="2770c-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="2770c-802">Jeśli w bazie danych nie ma studentów, baza danych zostanie zainicjowana z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="2770c-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="2770c-803">Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="2770c-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="2770c-804">`EnsureCreated`Metoda automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="2770c-805">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="2770c-806">W *program.cs*Zmień metodę, `Main` Aby wywołać `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="2770c-806">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="2770c-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2770c-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2770c-808">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="2770c-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2770c-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2770c-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2770c-810">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="2770c-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="2770c-811">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="2770c-811">View the DB</span></span>

<span data-ttu-id="2770c-812">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="2770c-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="2770c-813">W rezultacie nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="2770c-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="2770c-814">Identyfikator GUID będzie różny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2770c-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="2770c-815">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2770c-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="2770c-816">W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="2770c-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="2770c-817">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="2770c-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="2770c-818">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="2770c-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="2770c-819">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="2770c-819">Asynchronous code</span></span>

<span data-ttu-id="2770c-820">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="2770c-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2770c-821">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="2770c-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2770c-822">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="2770c-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2770c-823">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="2770c-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2770c-824">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="2770c-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2770c-825">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="2770c-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="2770c-826">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="2770c-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="2770c-827">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="2770c-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2770c-828">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="2770c-829">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="2770c-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="2770c-830">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="2770c-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="2770c-831">Automatycznie Utwórz obiekt [zadania](/dotnet/api/system.threading.tasks.task) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="2770c-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="2770c-832">Aby uzyskać więcej informacji, zobacz [Typ zwracany zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="2770c-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="2770c-833">Niejawny typ zwracany `Task` reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="2770c-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="2770c-834">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="2770c-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2770c-835">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2770c-836">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2770c-837">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2770c-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2770c-838">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="2770c-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="2770c-839">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="2770c-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="2770c-840">Obejmuje, `ToListAsync` ,, `SingleOrDefaultAsync` `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="2770c-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2770c-841">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="2770c-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2770c-842">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="2770c-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="2770c-843">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2770c-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="2770c-844">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="2770c-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="2770c-845">W następnym samouczku są badane podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="2770c-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="2770c-846">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2770c-846">Additional resources</span></span>

* [<span data-ttu-id="2770c-847">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="2770c-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="2770c-848">Dalej</span><span class="sxs-lookup"><span data-stu-id="2770c-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
