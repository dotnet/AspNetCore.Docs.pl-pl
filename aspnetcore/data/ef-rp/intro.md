---
title: Strony brzytwy z entity framework core w ASP.NET Core - Samouczek 1 z 8
author: rick-anderson
description: Pokazuje, jak utworzyć aplikację Razor Pages przy użyciu entity framework core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656822"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="5d6af-103">Strony brzytwy z entity framework core w ASP.NET Core - Samouczek 1 z 8</span><span class="sxs-lookup"><span data-stu-id="5d6af-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="5d6af-104">Przez [Tom Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d6af-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d6af-105">Jest to pierwszy z serii samouczków, które pokazują, jak korzystać z entity framework (EF) Core w [aplikacji ASP.NET Core Razor Pages.](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="5d6af-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="5d6af-106">Samouczki kompilacji witryny sieci web dla fikcyjnego Contoso University.</span><span class="sxs-lookup"><span data-stu-id="5d6af-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="5d6af-107">Witryna zawiera funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora.</span><span class="sxs-lookup"><span data-stu-id="5d6af-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="5d6af-108">Samouczek używa pierwszego podejścia kodu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="5d6af-109">Aby uzyskać informacje na temat po tym samouczku przy użyciu pierwszej bazy danych podejście, zobacz [ten problem Github](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="5d6af-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="5d6af-110">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d6af-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="5d6af-111">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5d6af-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5d6af-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5d6af-112">Prerequisites</span></span>

* <span data-ttu-id="5d6af-113">Jeśli dopiero zaczynasz pracę na stronach Razor Pages, przed rozpoczęciem tej serii przejdź do serii samouczków Wprowadzenie do [stron razor.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="5d6af-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="5d6af-116">Aparaty baz danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-116">Database engines</span></span>

<span data-ttu-id="5d6af-117">Instrukcje programu Visual Studio używają [programu SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji programu SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="5d6af-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="5d6af-118">Instrukcje kodu programu Visual Studio używają [SQLite](https://www.sqlite.org/), międzyplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="5d6af-119">Jeśli zdecydujesz się korzystać z SQLite, pobierz i zainstaluj narzędzie innej firmy do zarządzania i przeglądania bazy danych SQLite, takiej jak [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="5d6af-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5d6af-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5d6af-120">Troubleshooting</span></span>

<span data-ttu-id="5d6af-121">Jeśli napotkasz problem, którego nie możesz rozwiązać, porównaj kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="5d6af-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="5d6af-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com, użycie [tagu core ASP.NET](https://stackoverflow.com/questions/tagged/asp.net-core) lub [znacznika EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="5d6af-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="5d6af-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="5d6af-123">The sample app</span></span>

<span data-ttu-id="5d6af-124">Aplikacja wbudowana w te samouczki jest podstawową stroną uniwersytecką.</span><span class="sxs-lookup"><span data-stu-id="5d6af-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="5d6af-125">Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach.</span><span class="sxs-lookup"><span data-stu-id="5d6af-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="5d6af-126">Oto kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="5d6af-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona Indeks uczniów](intro/_static/students-index30.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit30.png)

<span data-ttu-id="5d6af-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="5d6af-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="5d6af-130">Samouczek koncentruje się na tym, jak używać EF Core, a nie jak dostosować interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5d6af-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="5d6af-131">Kliknij łącze w górnej części strony, aby uzyskać kod źródłowy ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="5d6af-132">Folder *cu30* zawiera kod ASP.NET wersji Core 3.0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="5d6af-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="5d6af-133">Pliki, które odzwierciedlają stan kodu dla tutoriali 1-7 można znaleźć w folderze *cu30snapshots.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-134">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d6af-135">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="5d6af-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="5d6af-136">Usuń trzy pliki i jeden folder z *elementem SQLite* w nazwie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="5d6af-137">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5d6af-137">Build the project.</span></span>
* <span data-ttu-id="5d6af-138">W konsoli Menedżera pakietów (PMC) uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5d6af-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="5d6af-139">Uruchom projekt do wysiewu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d6af-141">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="5d6af-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="5d6af-142">Usuń *plik ContosoUniversity.csproj*i zmień nazwę *pliku ContosoUniversitySQLite.csproj* na *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="5d6af-143">Usuń *Startup.cs*i zmień nazwę *StartupSQLite.cs* *na Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="5d6af-144">Usuń *appSettings.json*i zmień nazwę *aplikacjiSettingsSQLite.json* na *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="5d6af-145">Usuń folder *Migracje* i zmień nazwę *MigrationsSQL* na *Migracje*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="5d6af-146">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5d6af-146">Build the project.</span></span>
* <span data-ttu-id="5d6af-147">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5d6af-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="5d6af-148">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="5d6af-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="5d6af-149">Uruchom projekt do wysiewu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="5d6af-150">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="5d6af-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-151">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d6af-152">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5d6af-153">Wybierz **ASP.NET podstawową aplikację sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5d6af-154">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="5d6af-155">Ważne jest, aby użyć tej dokładnej nazwy, w tym wielkich liter, więc obszary nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="5d6af-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="5d6af-156">Wybierz pozycję **.NET Core** i **ASP.NET Core 3.0** w menu rozwijanym, a następnie wybierz pozycję **Aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5d6af-158">W terminalu przejdź do folderu, w którym należy utworzyć folder projektu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="5d6af-159">Uruchom następujące polecenia, aby utworzyć projekt `cd` Razor Pages i do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="5d6af-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="5d6af-160">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="5d6af-160">Set up the site style</span></span>

<span data-ttu-id="5d6af-161">Skonfiguruj nagłówek witryny, stopkę i menu, aktualizując *pages/shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5d6af-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="5d6af-162">Zmień każde wystąpienie "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="5d6af-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="5d6af-163">Istnieją trzy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="5d6af-163">There are three occurrences.</span></span>

* <span data-ttu-id="5d6af-164">Usuń wpisy menu **Strona główna** i **Prywatność** oraz dodaj wpisy dla **informacji**, **Studenci**, **Kursy,** **Instruktorzy**i **Działy**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="5d6af-165">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="5d6af-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="5d6af-166">W *Pages/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET Core tekstem o tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5d6af-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="5d6af-167">Uruchom aplikację, aby sprawdzić, czy jest wyświetlana strona główna.</span><span class="sxs-lookup"><span data-stu-id="5d6af-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="5d6af-168">Model danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-168">The data model</span></span>

<span data-ttu-id="5d6af-169">Następujące sekcje tworzą model danych:</span><span class="sxs-lookup"><span data-stu-id="5d6af-169">The following sections create a data model:</span></span>

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

<span data-ttu-id="5d6af-171">Uczeń może zapisać się na dowolną liczbę kursów, a kurs może mieć dowolną liczbę studentów.</span><span class="sxs-lookup"><span data-stu-id="5d6af-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="5d6af-172">Jednostka Student</span><span class="sxs-lookup"><span data-stu-id="5d6af-172">The Student entity</span></span>

![Diagram encji ucznia](intro/_static/student-entity.png)

* <span data-ttu-id="5d6af-174">Utwórz folder *Modele* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="5d6af-175">Utwórz *modele/student.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="5d6af-176">Właściwość `ID` staje się kolumną klucza podstawowego tabeli bazy danych, która odpowiada tej klasie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="5d6af-177">Domyślnie EF Core interpretuje właściwość, `ID` `classnameID` która jest nazwany lub jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="5d6af-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="5d6af-178">Tak więc alternatywą automatycznie `Student` rozpoznawaną nazwę `StudentID`dla klasy klucz podstawowy jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="5d6af-179">Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5d6af-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="5d6af-180">Właściwości nawigacji przechowują inne jednostki, które są powiązane z tą encją.</span><span class="sxs-lookup"><span data-stu-id="5d6af-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="5d6af-181">W takim przypadku `Enrollments` właściwość `Student` jednostki posiada `Enrollment` wszystkie jednostki, które są związane z tym Student.</span><span class="sxs-lookup"><span data-stu-id="5d6af-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="5d6af-182">Na przykład jeśli wiersz studenta w bazie danych ma `Enrollments` dwa powiązane wiersze rejestracji, właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="5d6af-183">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora ucznia.</span><span class="sxs-lookup"><span data-stu-id="5d6af-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="5d6af-184">Załóżmy na przykład, że wiersz studenta ma identyfikator=1.</span><span class="sxs-lookup"><span data-stu-id="5d6af-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="5d6af-185">Powiązane wiersze rejestracji będą miały StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="5d6af-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="5d6af-186">StudentID jest *kluczem obcym* w tabeli Rejestracja.</span><span class="sxs-lookup"><span data-stu-id="5d6af-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="5d6af-187">Właściwość `Enrollments` jest `ICollection<Enrollment>` zdefiniowana jako ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="5d6af-188">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="5d6af-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="5d6af-189">Gdy `ICollection<Enrollment>` jest używany, EF `HashSet<Enrollment>` Core tworzy kolekcję domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="5d6af-190">Jednostka Rejestracji</span><span class="sxs-lookup"><span data-stu-id="5d6af-190">The Enrollment entity</span></span>

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="5d6af-192">Utwórz *modele/enrollment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="5d6af-193">Właściwość `EnrollmentID` jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca `ID` zamiast samego.</span><span class="sxs-lookup"><span data-stu-id="5d6af-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="5d6af-194">W przypadku modelu danych produkcyjnych wybierz jeden wzorzec i użyj go konsekwentnie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="5d6af-195">Ten samouczek używa zarówno tylko do zilustrowania, że oba działają.</span><span class="sxs-lookup"><span data-stu-id="5d6af-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="5d6af-196">Korzystanie `ID` `classname` bez ułatwia implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="5d6af-197">Obiekt `Grade` jest `enum`własnością .</span><span class="sxs-lookup"><span data-stu-id="5d6af-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="5d6af-198">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="5d6af-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="5d6af-199">Stopień, który jest null różni się&mdash;od zerowej klasy null oznacza, że klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="5d6af-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="5d6af-200">Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="5d6af-201">Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, `Student` więc właściwość zawiera jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="5d6af-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="5d6af-202">Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="5d6af-203">Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.</span><span class="sxs-lookup"><span data-stu-id="5d6af-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="5d6af-204">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwany `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5d6af-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="5d6af-205">Na przykład`StudentID` jest kluczem `Student` obcym właściwości `Student` nawigacji, ponieważ kluczem podstawowym jednostki jest `ID`.</span><span class="sxs-lookup"><span data-stu-id="5d6af-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="5d6af-206">Właściwości klucza obcego `<primary key property name>`mogą być również nazwane .</span><span class="sxs-lookup"><span data-stu-id="5d6af-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="5d6af-207">Na przykład, `CourseID` `Course` ponieważ kluczem podstawowym `CourseID`jednostki jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="5d6af-208">Jednostka Kurs</span><span class="sxs-lookup"><span data-stu-id="5d6af-208">The Course entity</span></span>

![Diagram encji kursu](intro/_static/course-entity.png)

<span data-ttu-id="5d6af-210">Utwórz *modele/course.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="5d6af-211">Właściwość `Enrollments` jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="5d6af-212">Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.</span><span class="sxs-lookup"><span data-stu-id="5d6af-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="5d6af-213">Atrybut `DatabaseGenerated` umożliwia aplikacji określić klucz podstawowy, a nie o bazy danych wygenerować go.</span><span class="sxs-lookup"><span data-stu-id="5d6af-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="5d6af-214">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="5d6af-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="5d6af-215">Strony dla uczniów rusztowania</span><span class="sxs-lookup"><span data-stu-id="5d6af-215">Scaffold Student pages</span></span>

<span data-ttu-id="5d6af-216">W tej sekcji służy ASP.NET narzędzie do tworzenia rusztowań rdzenia do generowania:</span><span class="sxs-lookup"><span data-stu-id="5d6af-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="5d6af-217">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="5d6af-217">An EF Core *context* class.</span></span> <span data-ttu-id="5d6af-218">Kontekst jest klasą główną, która koordynuje funkcjonalność entity framework dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="5d6af-219">Pochodzi z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="5d6af-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="5d6af-220">Strony maszynki do golenia obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` encji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-221">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d6af-222">Tworzenie folderu *Uczniowie* w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="5d6af-223">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Strony/Uczniowie* i wybierz pozycję **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5d6af-224">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="5d6af-225">W oknie dialogowym **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="5d6af-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="5d6af-226">Z **listy** rozwijanej Klasa Model wybierz pozycję **Uczeń (ContosoUniversity.Models).**</span><span class="sxs-lookup"><span data-stu-id="5d6af-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="5d6af-227">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus).</span><span class="sxs-lookup"><span data-stu-id="5d6af-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="5d6af-228">Zmień nazwę kontekstu danych z *ContosoUniversity.Models.ContosoUniversityContext* na *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="5d6af-229">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-229">Select **Add**.</span></span>

<span data-ttu-id="5d6af-230">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="5d6af-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5d6af-232">Uruchom następujące polecenia .NET Core CLI, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="5d6af-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="5d6af-233">Pakiet Microsoft.VisualStudio.Web.CodeGeneration.Design jest wymagany do tworzenia rusztowań.</span><span class="sxs-lookup"><span data-stu-id="5d6af-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="5d6af-234">Mimo że aplikacja nie będzie używać programu SQL Server, narzędzie do tworzenia szkieletów wymaga pakietu programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5d6af-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="5d6af-235">Tworzenie folderu *Strony/Uczniowie.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="5d6af-236">Uruchom następujące polecenie, aby zainstalować [narzędzie do tworzenia rusztowań aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="5d6af-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="5d6af-237">Uruchom następujące polecenie do tworzenia szkieletów stron uczniów.</span><span class="sxs-lookup"><span data-stu-id="5d6af-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="5d6af-238">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="5d6af-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="5d6af-239">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="5d6af-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="5d6af-240">Jeśli masz problem z poprzednim krokiem, skompiluj projekt i ponów próbę kroku szkieletu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="5d6af-241">Proces rusztowania:</span><span class="sxs-lookup"><span data-stu-id="5d6af-241">The scaffolding process:</span></span>

* <span data-ttu-id="5d6af-242">Tworzy strony Razor w folderze *Strony/Uczniowie:*</span><span class="sxs-lookup"><span data-stu-id="5d6af-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="5d6af-243">*Create.cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5d6af-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="5d6af-244">*Usuń.cshtml* i *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5d6af-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="5d6af-245">*Details.cshtml* i *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5d6af-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="5d6af-246">*Edit.cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5d6af-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="5d6af-247">*Index.cshtml* i *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5d6af-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="5d6af-248">Tworzy *dane/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="5d6af-249">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="5d6af-250">Dodaje ciąg połączenia bazy danych do *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="5d6af-251">Parametry połączenia bazy danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-252">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d6af-253">Parametry połączenia określają [usługę SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="5d6af-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="5d6af-254">LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="5d6af-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="5d6af-255">Domyślnie LocalDB tworzy pliki *mdf* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d6af-257">Zmień parametry połączenia, aby wskazać plik bazy danych SQLite o nazwie *CU.db:*</span><span class="sxs-lookup"><span data-stu-id="5d6af-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="5d6af-258">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-258">Update the database context class</span></span>

<span data-ttu-id="5d6af-259">Klasą główną, która koordynuje funkcje EF Core dla danego modelu danych jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="5d6af-260">Kontekst pochodzi od pliku [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5d6af-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5d6af-261">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="5d6af-262">W tym projekcie klasa `SchoolContext`nosi nazwę .</span><span class="sxs-lookup"><span data-stu-id="5d6af-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="5d6af-263">Zaktualizuj *SchoolContext.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="5d6af-264">Wyróżniony kod tworzy [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwości dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="5d6af-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="5d6af-265">W terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="5d6af-265">In EF Core terminology:</span></span>

* <span data-ttu-id="5d6af-266">Zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="5d6af-267">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="5d6af-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5d6af-268">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości DBSet powinny być nazwami mnogimi.</span><span class="sxs-lookup"><span data-stu-id="5d6af-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="5d6af-269">Ponieważ narzędzie rusztowania`Student` utworzyło zestaw DBSet, ten `Students`krok zmienia go na mnogą .</span><span class="sxs-lookup"><span data-stu-id="5d6af-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="5d6af-270">Aby kod Razor Pages był zgodny z nową nazwą DBSet, `_context.Student` dokonaj globalnej zmiany w całym projekcie na `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="5d6af-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="5d6af-271">Istnieje 8 zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5d6af-271">There are 8 occurrences.</span></span>

<span data-ttu-id="5d6af-272">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="5d6af-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="5d6af-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="5d6af-273">Startup.cs</span></span>

<span data-ttu-id="5d6af-274">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5d6af-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5d6af-275">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5d6af-276">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="5d6af-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5d6af-277">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="5d6af-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5d6af-278">Narzędzie szkieletu automatycznie zarejestrował klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="5d6af-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-279">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d6af-280">W `ConfigureServices`, wyróżnione linie zostały dodane przez rusztowania:</span><span class="sxs-lookup"><span data-stu-id="5d6af-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5d6af-282">W `ConfigureServices`, upewnij się, że kod dodany `UseSqlite`przez wywołanie rusztowania .</span><span class="sxs-lookup"><span data-stu-id="5d6af-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="5d6af-283">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5d6af-284">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="5d6af-285">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-285">Create the database</span></span>

<span data-ttu-id="5d6af-286">Zaktualizuj *Program.cs,* aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="5d6af-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="5d6af-287">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) Metoda nie wykonuje żadnych akcji, jeśli baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="5d6af-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="5d6af-288">Jeśli nie istnieje baza danych, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="5d6af-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="5d6af-289">`EnsureCreated`umożliwia następujące przepływ pracy do obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="5d6af-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="5d6af-290">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-290">Delete the database.</span></span> <span data-ttu-id="5d6af-291">Wszystkie istniejące dane są tracone.</span><span class="sxs-lookup"><span data-stu-id="5d6af-291">Any existing data is lost.</span></span>
* <span data-ttu-id="5d6af-292">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-292">Change the data model.</span></span> <span data-ttu-id="5d6af-293">Na przykład dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="5d6af-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="5d6af-294">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d6af-294">Run the app.</span></span>
* <span data-ttu-id="5d6af-295">`EnsureCreated`tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="5d6af-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="5d6af-296">Ten przepływ pracy działa dobrze na wczesnym etapie rozwoju, gdy schemat szybko ewoluuje, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="5d6af-297">Sytuacja jest inna, gdy dane, które zostały wprowadzone do bazy danych musi zostać zachowana.</span><span class="sxs-lookup"><span data-stu-id="5d6af-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="5d6af-298">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="5d6af-299">W dalszej części serii samouczków można `EnsureCreated` usunąć bazę danych, która została utworzona przez i zamiast tego używać migracji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="5d6af-300">Nie można zaktualizować `EnsureCreated` bazy danych utworzonej przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="5d6af-301">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="5d6af-301">Test the app</span></span>

* <span data-ttu-id="5d6af-302">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d6af-302">Run the app.</span></span>
* <span data-ttu-id="5d6af-303">Wybierz łącze **Uczniowie,** a następnie **utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="5d6af-304">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="5d6af-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="5d6af-305">Seed bazy danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-305">Seed the database</span></span>

<span data-ttu-id="5d6af-306">Metoda `EnsureCreated` tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="5d6af-307">W tej sekcji dodano kod, który wypełnia bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="5d6af-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="5d6af-308">Utwórz *plik Data/DbInitializer.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="5d6af-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="5d6af-309">Kod sprawdza, czy w bazie danych są uczniowie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="5d6af-310">Jeśli nie ma żadnych studentów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="5d6af-311">Tworzy dane testowe w tablicach, `List<T>` a nie w kolekcjach w celu optymalizacji wydajności.</span><span class="sxs-lookup"><span data-stu-id="5d6af-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="5d6af-312">W *Program.cs*, zastąp `EnsureCreated` `DbInitializer.Initialize` połączenie połączeniem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-313">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d6af-314">Zatrzymaj aplikację, jeśli jest uruchomiona, i uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="5d6af-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5d6af-316">Zatrzymaj aplikację, jeśli jest uruchomiona, i usuń plik *CU.db.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="5d6af-317">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d6af-317">Restart the app.</span></span>

* <span data-ttu-id="5d6af-318">Wybierz stronę Uczniowie, aby wyświetlić rozstawione dane.</span><span class="sxs-lookup"><span data-stu-id="5d6af-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="5d6af-319">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-320">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d6af-321">Otwórz **program SQL Server Object Explorer** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5d6af-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="5d6af-322">W obszarze SSOX wybierz **(localdb)\MSSQLLocalDB > databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="5d6af-323">Nazwa bazy danych jest generowana na podstawie nazwy kontekstu podanej wcześniej oraz myślnika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="5d6af-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="5d6af-324">Rozwiń węzeł **Tabele.**</span><span class="sxs-lookup"><span data-stu-id="5d6af-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="5d6af-325">Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="5d6af-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="5d6af-326">Kliknij prawym przyciskiem myszy tabelę **Student** `Student` i kliknij `Student` polecenie **Wyświetl kod,** aby zobaczyć, jak model jest mapowany do schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="5d6af-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d6af-328">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane rozstawione.</span><span class="sxs-lookup"><span data-stu-id="5d6af-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="5d6af-329">Plik bazy danych nosi nazwę *CU.db* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="5d6af-330">Kod asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="5d6af-330">Asynchronous code</span></span>

<span data-ttu-id="5d6af-331">Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="5d6af-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="5d6af-332">Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane.</span><span class="sxs-lookup"><span data-stu-id="5d6af-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="5d6af-333">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="5d6af-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="5d6af-334">Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć.</span><span class="sxs-lookup"><span data-stu-id="5d6af-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="5d6af-335">Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="5d6af-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="5d6af-336">W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer może obsługiwać większy ruch bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="5d6af-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="5d6af-337">Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="5d6af-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="5d6af-338">W sytuacjach o niskim natężeniu ruchu osiągi są znikome, podczas gdy w przypadku sytuacji o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.</span><span class="sxs-lookup"><span data-stu-id="5d6af-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="5d6af-339">W poniższym kodzie [asynchroniczne](/dotnet/csharp/language-reference/keywords/async) słowo kluczowe, `Task<T>` zwracana wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="5d6af-340">Słowo `async` kluczowe mówi kompilatorowi:</span><span class="sxs-lookup"><span data-stu-id="5d6af-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="5d6af-341">Generowanie wywołań zwrotnych dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="5d6af-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="5d6af-342">Utwórz obiekt [Zadanie,](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="5d6af-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="5d6af-343">Typ `Task<T>` zwracania reprezentuje bieżącą pracę.</span><span class="sxs-lookup"><span data-stu-id="5d6af-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="5d6af-344">Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="5d6af-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="5d6af-345">Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="5d6af-346">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="5d6af-347">`ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="5d6af-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="5d6af-348">Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="5d6af-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="5d6af-349">Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="5d6af-350">Obejmuje `ToListAsync`to `SingleOrDefaultAsync` `FirstOrDefaultAsync`, `SaveChangesAsync`, i .</span><span class="sxs-lookup"><span data-stu-id="5d6af-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="5d6af-351">Nie zawiera instrukcji, które po `IQueryable`prostu `var students = context.Students.Where(s => s.LastName == "Davolio")`zmienić , takich jak .</span><span class="sxs-lookup"><span data-stu-id="5d6af-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="5d6af-352">Kontekst EF Core nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle.</span><span class="sxs-lookup"><span data-stu-id="5d6af-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="5d6af-353">Aby skorzystać z zalet wydajności kodu asynchronii, sprawdź, czy pakiety biblioteki (takie jak do stronicowania) używają asynchronii, jeśli wywołują metody EF Core, które wysyłają zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="5d6af-354">Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne za pomocą asynchronii i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="5d6af-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5d6af-355">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="5d6af-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5d6af-356">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="5d6af-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5d6af-357">Przykładowa aplikacja internetowa Contoso University pokazuje, jak utworzyć aplikację ASP.NET Core Razor Pages przy użyciu programu Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="5d6af-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="5d6af-358">Przykładowa aplikacja jest witryną sieci web fikcyjnego Uniwersytetu Contoso.</span><span class="sxs-lookup"><span data-stu-id="5d6af-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="5d6af-359">Obejmuje funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora.</span><span class="sxs-lookup"><span data-stu-id="5d6af-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="5d6af-360">Ta strona jest pierwszą z serii samouczków, które wyjaśniają, jak utworzyć przykładową aplikację Uniwersytetu Contoso.</span><span class="sxs-lookup"><span data-stu-id="5d6af-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="5d6af-361">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d6af-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="5d6af-362">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5d6af-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5d6af-363">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5d6af-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-364">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="5d6af-366">Znajomość [stron Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="5d6af-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="5d6af-367">Nowi programiści powinni [ukończyć pracę z razor Pages](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="5d6af-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5d6af-368">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5d6af-368">Troubleshooting</span></span>

<span data-ttu-id="5d6af-369">Jeśli napotkasz problem, którego nie możesz rozwiązać, zazwyczaj możesz znaleźć rozwiązanie, porównując kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="5d6af-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="5d6af-370">Dobrym sposobem na uzyskanie pomocy jest opublikowanie pytania do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="5d6af-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="5d6af-371">Aplikacja internetowa Uniwersytetu Contoso</span><span class="sxs-lookup"><span data-stu-id="5d6af-371">The Contoso University web app</span></span>

<span data-ttu-id="5d6af-372">Aplikacja wbudowana w te samouczki jest podstawową stroną uniwersytecką.</span><span class="sxs-lookup"><span data-stu-id="5d6af-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="5d6af-373">Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach.</span><span class="sxs-lookup"><span data-stu-id="5d6af-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="5d6af-374">Oto kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="5d6af-374">Here are a few of the screens created in the tutorial.</span></span>

![Strona Indeks uczniów](intro/_static/students-index.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit.png)

<span data-ttu-id="5d6af-377">Styl interfejsu użytkownika tej witryny jest zbliżony do tego, co jest generowane przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="5d6af-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="5d6af-378">Samouczek koncentruje się na EF Core ze stronami Razor, a nie interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5d6af-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="5d6af-379">Tworzenie aplikacji internetowej Strony razorów ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="5d6af-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-380">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d6af-381">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5d6af-382">Utwórz nową ASP.NET podstawową aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5d6af-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="5d6af-383">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="5d6af-384">Ważne jest, aby nadać nazwę projektowi *ContosoUniversity,* aby obszary nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="5d6af-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="5d6af-385">Wybierz **ASP.NET Core 2.1** w menu rozwijanym, a następnie wybierz pozycję **Aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="5d6af-386">Aby zapoznać się z obrazami powyższych kroków, zobacz [Tworzenie aplikacji internetowej Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="5d6af-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="5d6af-387">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d6af-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="5d6af-389">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="5d6af-389">Set up the site style</span></span>

<span data-ttu-id="5d6af-390">Kilka zmian skonfigurować menu witryny, układ i stronę główną.</span><span class="sxs-lookup"><span data-stu-id="5d6af-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="5d6af-391">Aktualizuj *strony/udostępnione/_Layout.cshtml* o następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5d6af-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="5d6af-392">Zmień każde wystąpienie "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="5d6af-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="5d6af-393">Istnieją trzy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="5d6af-393">There are three occurrences.</span></span>

* <span data-ttu-id="5d6af-394">Dodaj pozycje menu dla **studentów,** **kursów,** **instruktorów**i **działów**i usuń wpis menu **Kontakt.**</span><span class="sxs-lookup"><span data-stu-id="5d6af-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="5d6af-395">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="5d6af-395">The changes are highlighted.</span></span> <span data-ttu-id="5d6af-396">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="5d6af-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="5d6af-397">W *Pages/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET i MVC tekstem o tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5d6af-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="5d6af-398">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-398">Create the data model</span></span>

<span data-ttu-id="5d6af-399">Tworzenie klas jednostek dla aplikacji Contoso University.</span><span class="sxs-lookup"><span data-stu-id="5d6af-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="5d6af-400">Zacznij od następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="5d6af-400">Start with the following three entities:</span></span>

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

<span data-ttu-id="5d6af-402">Istnieje relacja jeden do wielu między `Student` `Enrollment` jednostkami i jednostkami.</span><span class="sxs-lookup"><span data-stu-id="5d6af-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="5d6af-403">Istnieje relacja jeden do wielu między `Course` `Enrollment` jednostkami i jednostkami.</span><span class="sxs-lookup"><span data-stu-id="5d6af-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="5d6af-404">Student może zapisać się na dowolną liczbę kursów.</span><span class="sxs-lookup"><span data-stu-id="5d6af-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="5d6af-405">Kurs może mieć dowolną liczbę studentów zapisanych w nim.</span><span class="sxs-lookup"><span data-stu-id="5d6af-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="5d6af-406">W poniższych sekcjach tworzona jest klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5d6af-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="5d6af-407">Jednostka Student</span><span class="sxs-lookup"><span data-stu-id="5d6af-407">The Student entity</span></span>

![Diagram encji ucznia](intro/_static/student-entity.png)

<span data-ttu-id="5d6af-409">Utwórz folder *Modele.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-409">Create a *Models* folder.</span></span> <span data-ttu-id="5d6af-410">W folderze *Modele* utwórz plik klasy o nazwie *Student.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="5d6af-411">Właściwość `ID` staje się kolumną klucza podstawowego tabeli bazy danych (DB), która odpowiada tej klasie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="5d6af-412">Domyślnie EF Core interpretuje właściwość, `ID` `classnameID` która jest nazwany lub jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="5d6af-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="5d6af-413">W `classnameID` `classname` , jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="5d6af-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="5d6af-414">Alternatywny automatycznie rozpoznawany klucz `StudentID` podstawowy znajduje się w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="5d6af-415">Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5d6af-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="5d6af-416">Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą encją.</span><span class="sxs-lookup"><span data-stu-id="5d6af-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="5d6af-417">W takim przypadku `Enrollments` właściwość `Student entity` posiada wszystkie `Enrollment` podmioty, które są `Student`z tym powiązane .</span><span class="sxs-lookup"><span data-stu-id="5d6af-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="5d6af-418">Na przykład jeśli wiersz studenta w bazy danych ma `Enrollments` dwa powiązane wiersze rejestracji, właściwość nawigacji zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="5d6af-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="5d6af-419">Pokrewny `Enrollment` wiersz to wiersz zawierający podstawową `StudentID` wartość klucza ucznia w kolumnie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="5d6af-420">Załóżmy na przykład, że uczeń z identyfikatorem=1 ma dwa wiersze w `Enrollment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="5d6af-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="5d6af-421">Tabela `Enrollment` ma dwa `StudentID` wiersze z = 1.</span><span class="sxs-lookup"><span data-stu-id="5d6af-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="5d6af-422">`StudentID`jest kluczem obcym w `Enrollment` tabeli, `Student` który określa ucznia w tabeli.</span><span class="sxs-lookup"><span data-stu-id="5d6af-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="5d6af-423">Jeśli właściwość nawigacji może pomieścić wiele jednostek, właściwość nawigacji `ICollection<T>`musi być typem listy, na przykład .</span><span class="sxs-lookup"><span data-stu-id="5d6af-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="5d6af-424">`ICollection<T>`można określić lub typ, `List<T>` `HashSet<T>`taki jak lub .</span><span class="sxs-lookup"><span data-stu-id="5d6af-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="5d6af-425">Gdy `ICollection<T>` jest używany, EF `HashSet<T>` Core tworzy kolekcję domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="5d6af-426">Właściwości nawigacji, które przechowują wiele jednostek pochodzą z relacji wiele do wielu i jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="5d6af-427">Jednostka Rejestracji</span><span class="sxs-lookup"><span data-stu-id="5d6af-427">The Enrollment entity</span></span>

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="5d6af-429">W folderze *Modele* utwórz *Enrollment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="5d6af-430">Właściwość `EnrollmentID` jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="5d6af-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="5d6af-431">Ta jednostka używa `classnameID` wzorca, `ID` a `Student` nie jak jednostki.</span><span class="sxs-lookup"><span data-stu-id="5d6af-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="5d6af-432">Zazwyczaj deweloperzy wybrać jeden wzorzec i używać go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="5d6af-433">W późniejszym samouczku przy użyciu identyfikatora bez nazwy klasy jest wyświetlany, aby ułatwić implementowanie dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="5d6af-434">Obiekt `Grade` jest `enum`własnością .</span><span class="sxs-lookup"><span data-stu-id="5d6af-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="5d6af-435">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest nullable.</span><span class="sxs-lookup"><span data-stu-id="5d6af-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="5d6af-436">Stopień, który ma wartość null, różni się od klasy zerowej — wartość null oznacza, że ocena nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="5d6af-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="5d6af-437">Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="5d6af-438">Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, `Student` więc właściwość zawiera jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="5d6af-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="5d6af-439">Jednostka `Student` różni się `Student.Enrollments` od właściwości nawigacji, `Enrollment` która zawiera wiele jednostek.</span><span class="sxs-lookup"><span data-stu-id="5d6af-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="5d6af-440">Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="5d6af-441">Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.</span><span class="sxs-lookup"><span data-stu-id="5d6af-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="5d6af-442">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwany `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5d6af-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="5d6af-443">Na przykład`StudentID` dla `Student` właściwości nawigacji, `Student` ponieważ kluczem `ID`podstawowym jednostki jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="5d6af-444">Właściwości klucza obcego `<primary key property name>`mogą być również nazwane .</span><span class="sxs-lookup"><span data-stu-id="5d6af-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="5d6af-445">Na przykład, `CourseID` `Course` ponieważ kluczem podstawowym `CourseID`jednostki jest .</span><span class="sxs-lookup"><span data-stu-id="5d6af-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="5d6af-446">Jednostka Kurs</span><span class="sxs-lookup"><span data-stu-id="5d6af-446">The Course entity</span></span>

![Diagram encji kursu](intro/_static/course-entity.png)

<span data-ttu-id="5d6af-448">W folderze *Modele* utwórz *Course.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="5d6af-449">Właściwość `Enrollments` jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="5d6af-450">Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.</span><span class="sxs-lookup"><span data-stu-id="5d6af-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="5d6af-451">Atrybut `DatabaseGenerated` umożliwia aplikacji określić klucz podstawowy, a nie o db wygenerować go.</span><span class="sxs-lookup"><span data-stu-id="5d6af-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="5d6af-452">Rusztowanie modelu studenta</span><span class="sxs-lookup"><span data-stu-id="5d6af-452">Scaffold the student model</span></span>

<span data-ttu-id="5d6af-453">W tej sekcji model studenta jest szkieletu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="5d6af-454">Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="5d6af-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="5d6af-455">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5d6af-455">Build the project.</span></span>
* <span data-ttu-id="5d6af-456">Utwórz folder *Strony/Uczniowie.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-457">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d6af-458">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Strony/Uczniowie* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5d6af-459">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="5d6af-460">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="5d6af-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5d6af-461">Z **listy** rozwijanej Klasa Model wybierz pozycję **Uczeń (ContosoUniversity.Models).**</span><span class="sxs-lookup"><span data-stu-id="5d6af-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="5d6af-462">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zmień nazwę wygenerowaną na **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="5d6af-463">W **klasie kontekstu danych wybierz** pozycję **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="5d6af-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="5d6af-464">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-464">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="5d6af-466">Zobacz [Szkielet modelu filmu,](xref:tutorials/razor-pages/model#scaffold-the-movie-model) jeśli masz problem z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="5d6af-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d6af-468">Uruchom następujące polecenia, aby s rusztowania modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="5d6af-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="5d6af-469">Proces rusztowania utworzył i zmienił następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="5d6af-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5d6af-470">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="5d6af-470">Files created</span></span>

* <span data-ttu-id="5d6af-471">*Strony/studenci* Tworzenie, usuwanie, szczegóły, edycja, indeks.</span><span class="sxs-lookup"><span data-stu-id="5d6af-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="5d6af-472">*Dane/SzkołaContext.cs*</span><span class="sxs-lookup"><span data-stu-id="5d6af-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="5d6af-473">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="5d6af-473">File updates</span></span>

* <span data-ttu-id="5d6af-474">*Startup.cs* : Zmiany w tym pliku są szczegółowo opisane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="5d6af-475">*appsettings.json* : Ciąg połączenia używany do łączenia się z lokalną bazą danych jest dodawany.</span><span class="sxs-lookup"><span data-stu-id="5d6af-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5d6af-476">Badanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="5d6af-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5d6af-477">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5d6af-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5d6af-478">Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5d6af-479">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="5d6af-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5d6af-480">Kod konstruktora, który pobiera wystąpienie kontekstu db jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="5d6af-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5d6af-481">Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="5d6af-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5d6af-482">Sprawdź `ConfigureServices` metodę w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5d6af-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="5d6af-483">Podświetlona linia została dodana przez rusztowanie:</span><span class="sxs-lookup"><span data-stu-id="5d6af-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="5d6af-484">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5d6af-485">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="5d6af-486">Aktualizacja główna</span><span class="sxs-lookup"><span data-stu-id="5d6af-486">Update main</span></span>

<span data-ttu-id="5d6af-487">W *Program.cs*zmodyfikuj metodę, `Main` aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="5d6af-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="5d6af-488">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="5d6af-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="5d6af-489">Wywołanie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="5d6af-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="5d6af-490">Zutylizuj `EnsureCreated` kontekst po zakończeniu metody.</span><span class="sxs-lookup"><span data-stu-id="5d6af-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="5d6af-491">Poniższy kod przedstawia zaktualizowany plik *Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="5d6af-492">`EnsureCreated`zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="5d6af-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="5d6af-493">Jeśli istnieje, nie zostaną podjęte żadne działania.</span><span class="sxs-lookup"><span data-stu-id="5d6af-493">If it exists, no action is taken.</span></span> <span data-ttu-id="5d6af-494">Jeśli nie istnieje, a następnie bazy danych i wszystkie jego schematu są tworzone.</span><span class="sxs-lookup"><span data-stu-id="5d6af-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="5d6af-495">`EnsureCreated`nie używa migracji do tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="5d6af-496">Baza danych, która `EnsureCreated` jest tworzona z nie można później zaktualizować przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="5d6af-497">`EnsureCreated`jest wywoływana na uruchomienie aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="5d6af-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="5d6af-498">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-498">Delete the DB.</span></span>
* <span data-ttu-id="5d6af-499">Zmień schemat bazy danych (na przykład `EmailAddress` dodaj pole).</span><span class="sxs-lookup"><span data-stu-id="5d6af-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="5d6af-500">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d6af-500">Run the app.</span></span>
* <span data-ttu-id="5d6af-501">`EnsureCreated`tworzy bazę danych`EmailAddress` z kolumną.</span><span class="sxs-lookup"><span data-stu-id="5d6af-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="5d6af-502">`EnsureCreated`jest wygodne na wczesnym etapie rozwoju, gdy schemat szybko ewoluuje.</span><span class="sxs-lookup"><span data-stu-id="5d6af-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="5d6af-503">W dalszej części samouczka baza danych jest usuwana i używane są migracje.</span><span class="sxs-lookup"><span data-stu-id="5d6af-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="5d6af-504">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="5d6af-504">Test the app</span></span>

<span data-ttu-id="5d6af-505">Uruchom aplikację i zaakceptuj politykę plików cookie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="5d6af-506">Ta aplikacja nie przechowuje danych osobowych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="5d6af-507">Informacje na temat polityki dotyczącej plików cookie można znaleźć na [stronie pomocy w ogólnym rozporządzeniu o ochronie danych (RODO).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="5d6af-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="5d6af-508">Wybierz łącze **Uczniowie,** a następnie **utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="5d6af-509">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="5d6af-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="5d6af-510">Badanie kontekstu bazy danych SchoolContext</span><span class="sxs-lookup"><span data-stu-id="5d6af-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="5d6af-511">Klasą główną, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="5d6af-512">Kontekst danych pochodzi z pliku [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5d6af-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5d6af-513">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="5d6af-514">W tym projekcie klasa `SchoolContext`nosi nazwę .</span><span class="sxs-lookup"><span data-stu-id="5d6af-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="5d6af-515">Zaktualizuj *SchoolContext.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5d6af-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="5d6af-516">Wyróżniony kod tworzy [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwości dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="5d6af-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="5d6af-517">W terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="5d6af-517">In EF Core terminology:</span></span>

* <span data-ttu-id="5d6af-518">Zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="5d6af-519">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="5d6af-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5d6af-520">`DbSet<Enrollment>`i `DbSet<Course>` mogą zostać pominięte.</span><span class="sxs-lookup"><span data-stu-id="5d6af-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="5d6af-521">EF Core zawiera je `Student` niejawnie, `Enrollment` ponieważ jednostka `Enrollment` odwołuje się `Course` do jednostki, a jednostka odwołuje się do jednostki.</span><span class="sxs-lookup"><span data-stu-id="5d6af-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="5d6af-522">W tym samouczku, zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="5d6af-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="5d6af-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="5d6af-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="5d6af-524">Parametry połączenia określają [usługę SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="5d6af-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="5d6af-525">LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="5d6af-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="5d6af-526">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="5d6af-527">Domyślnie LocalDB tworzy pliki *.mdf* `C:/Users/<user>` DB w katalogu.</span><span class="sxs-lookup"><span data-stu-id="5d6af-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="5d6af-528">Dodaj kod, aby zainicjować bazę danych za pomocą danych testowych</span><span class="sxs-lookup"><span data-stu-id="5d6af-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="5d6af-529">EF Core tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="5d6af-530">W tej sekcji `Initialize` metoda jest zapisywana, aby wypełnić go danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="5d6af-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="5d6af-531">W folderze *Dane* utwórz nowy plik klasy o nazwie *DbInitializer.cs* i dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="5d6af-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="5d6af-532">Uwaga: Poprzedni kod używa `Models` dla obszaru`namespace ContosoUniversity.Models`nazw ( `Data`) zamiast .</span><span class="sxs-lookup"><span data-stu-id="5d6af-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="5d6af-533">`Models`jest zgodny z kodem wygenerowanym przez szkielety.</span><span class="sxs-lookup"><span data-stu-id="5d6af-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="5d6af-534">Aby uzyskać więcej informacji, zobacz [ten problem z rusztowaniami GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="5d6af-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="5d6af-535">Kod sprawdza, czy w pb.</span><span class="sxs-lookup"><span data-stu-id="5d6af-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="5d6af-536">Jeśli w db nie ma uczniów, baza danych jest inicjowana przy obliczu danych testowych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="5d6af-537">Ładuje dane testowe do `List<T>` tablic, a nie kolekcji w celu optymalizacji wydajności.</span><span class="sxs-lookup"><span data-stu-id="5d6af-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="5d6af-538">Metoda `EnsureCreated` automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="5d6af-539">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="5d6af-540">W *Program.cs*, zmodyfikuj `Main` metodę wywoływania: `Initialize`</span><span class="sxs-lookup"><span data-stu-id="5d6af-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="5d6af-541">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d6af-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d6af-542">Zatrzymaj aplikację, jeśli jest uruchomiona, i uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="5d6af-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d6af-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d6af-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5d6af-544">Zatrzymaj aplikację, jeśli jest uruchomiona, i usuń plik *CU.db.*</span><span class="sxs-lookup"><span data-stu-id="5d6af-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="5d6af-545">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="5d6af-545">View the DB</span></span>

<span data-ttu-id="5d6af-546">Nazwa bazy danych jest generowana na podstawie nazwy kontekstu podanej wcześniej oraz myślnika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="5d6af-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="5d6af-547">W związku z tym nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="5d6af-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="5d6af-548">Identyfikator GUID będzie inny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5d6af-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="5d6af-549">Otwórz **program SQL Server Object Explorer** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5d6af-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="5d6af-550">W obszarze SSOX kliknij **(localdb)\MSSQLLocalDB > databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="5d6af-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="5d6af-551">Rozwiń węzeł **Tabele.**</span><span class="sxs-lookup"><span data-stu-id="5d6af-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="5d6af-552">Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="5d6af-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="5d6af-553">Kod asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="5d6af-553">Asynchronous code</span></span>

<span data-ttu-id="5d6af-554">Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="5d6af-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="5d6af-555">Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane.</span><span class="sxs-lookup"><span data-stu-id="5d6af-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="5d6af-556">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="5d6af-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="5d6af-557">Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć.</span><span class="sxs-lookup"><span data-stu-id="5d6af-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="5d6af-558">Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="5d6af-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="5d6af-559">W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer jest włączony do obsługi większej liczby ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="5d6af-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="5d6af-560">Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="5d6af-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="5d6af-561">W sytuacjach o niskim natężeniu ruchu osiągi są znikome, podczas gdy w przypadku sytuacji o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.</span><span class="sxs-lookup"><span data-stu-id="5d6af-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="5d6af-562">W poniższym kodzie [asynchroniczne](/dotnet/csharp/language-reference/keywords/async) słowo kluczowe, `Task<T>` zwracana wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="5d6af-563">Słowo `async` kluczowe mówi kompilatorowi:</span><span class="sxs-lookup"><span data-stu-id="5d6af-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="5d6af-564">Generowanie wywołań zwrotnych dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="5d6af-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="5d6af-565">Automatycznie utwórz obiekt [Zadanie,](/dotnet/api/system.threading.tasks.task) który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="5d6af-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="5d6af-566">Aby uzyskać więcej informacji, zobacz [Typ zwrotu zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="5d6af-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="5d6af-567">Niejawny `Task` typ zwracany reprezentuje bieżącą pracę.</span><span class="sxs-lookup"><span data-stu-id="5d6af-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="5d6af-568">Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="5d6af-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="5d6af-569">Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="5d6af-570">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="5d6af-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="5d6af-571">`ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="5d6af-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="5d6af-572">Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="5d6af-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="5d6af-573">Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5d6af-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="5d6af-574">Obejmuje to `ToListAsync` `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`, i .</span><span class="sxs-lookup"><span data-stu-id="5d6af-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="5d6af-575">Nie zawiera instrukcji, które po `IQueryable`prostu `var students = context.Students.Where(s => s.LastName == "Davolio")`zmienić , takich jak .</span><span class="sxs-lookup"><span data-stu-id="5d6af-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="5d6af-576">Kontekst EF Core nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle.</span><span class="sxs-lookup"><span data-stu-id="5d6af-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="5d6af-577">Aby skorzystać z zalet wydajności kodu asynchronii, sprawdź, czy pakiety biblioteki (takie jak do stronicowania) używają asynchronii, jeśli wywołują metody EF Core, które wysyłają zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5d6af-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="5d6af-578">Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne za pomocą asynchronii i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="5d6af-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="5d6af-579">W następnym samouczku sprawdzane są podstawowe operacje CRUD (tworzenie, odczyt, aktualizacja, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="5d6af-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="5d6af-580">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5d6af-580">Additional resources</span></span>

* [<span data-ttu-id="5d6af-581">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="5d6af-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="5d6af-582">Dalej</span><span class="sxs-lookup"><span data-stu-id="5d6af-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
