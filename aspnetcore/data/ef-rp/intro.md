---
title: Strony brzytwy z entity framework core w ASP.NET Core - Samouczek 1 z 8
author: rick-anderson
description: Pokazuje, jak utworzyć aplikację Razor Pages przy użyciu entity framework core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 07faf5e596e7ea8b134d13caa0259c1e9d74ff1b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661615"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="5a0f2-103">Strony brzytwy z entity framework core w ASP.NET Core - Samouczek 1 z 8</span><span class="sxs-lookup"><span data-stu-id="5a0f2-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="5a0f2-104">Przez [Tom Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a0f2-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a0f2-105">Jest to pierwszy z serii samouczków, które pokazują, jak korzystać z entity framework (EF) Core w [aplikacji ASP.NET Core Razor Pages.](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="5a0f2-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="5a0f2-106">Samouczki kompilacji witryny sieci web dla fikcyjnego Contoso University.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="5a0f2-107">Witryna zawiera funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="5a0f2-108">Samouczek używa pierwszego podejścia kodu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="5a0f2-109">Aby uzyskać informacje na temat po tym samouczku przy użyciu pierwszej bazy danych podejście, zobacz [ten problem Github](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="5a0f2-110">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="5a0f2-111">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5a0f2-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5a0f2-112">Prerequisites</span></span>

* <span data-ttu-id="5a0f2-113">Jeśli dopiero zaczynasz pracę na stronach Razor Pages, przed rozpoczęciem tej serii przejdź do serii samouczków Wprowadzenie do [stron razor.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="5a0f2-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="5a0f2-116">Aparaty baz danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-116">Database engines</span></span>

<span data-ttu-id="5a0f2-117">Instrukcje programu Visual Studio używają [programu SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji programu SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="5a0f2-118">Instrukcje kodu programu Visual Studio używają [SQLite](https://www.sqlite.org/), międzyplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="5a0f2-119">Jeśli zdecydujesz się korzystać z SQLite, pobierz i zainstaluj narzędzie innej firmy do zarządzania i przeglądania bazy danych SQLite, takiej jak [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5a0f2-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5a0f2-120">Troubleshooting</span></span>

<span data-ttu-id="5a0f2-121">Jeśli napotkasz problem, którego nie możesz rozwiązać, porównaj kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="5a0f2-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="5a0f2-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com, użycie [tagu core ASP.NET](https://stackoverflow.com/questions/tagged/asp.net-core) lub [znacznika EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="5a0f2-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="5a0f2-123">The sample app</span></span>

<span data-ttu-id="5a0f2-124">Aplikacja wbudowana w te samouczki jest podstawową stroną uniwersytecką.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="5a0f2-125">Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="5a0f2-126">Oto kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona Indeks uczniów](intro/_static/students-index30.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit30.png)

<span data-ttu-id="5a0f2-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="5a0f2-130">Samouczek koncentruje się na tym, jak używać EF Core, a nie jak dostosować interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="5a0f2-131">Kliknij łącze w górnej części strony, aby uzyskać kod źródłowy ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="5a0f2-132">Folder *cu30* zawiera kod ASP.NET wersji Core 3.0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="5a0f2-133">Pliki, które odzwierciedlają stan kodu dla tutoriali 1-7 można znaleźć w folderze *cu30snapshots.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a0f2-135">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="5a0f2-136">Usuń trzy pliki i jeden folder z *elementem SQLite* w nazwie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="5a0f2-137">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-137">Build the project.</span></span>
* <span data-ttu-id="5a0f2-138">W konsoli Menedżera pakietów (PMC) uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="5a0f2-139">Uruchom projekt do wysiewu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a0f2-141">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="5a0f2-142">Usuń *plik ContosoUniversity.csproj*i zmień nazwę *pliku ContosoUniversitySQLite.csproj* na *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="5a0f2-143">Usuń *Startup.cs*i zmień nazwę *StartupSQLite.cs* *na Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="5a0f2-144">Usuń *appSettings.json*i zmień nazwę *aplikacjiSettingsSQLite.json* na *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="5a0f2-145">Usuń folder *Migracje* i zmień nazwę *MigrationsSQL* na *Migracje*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="5a0f2-146">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-146">Build the project.</span></span>
* <span data-ttu-id="5a0f2-147">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="5a0f2-148">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="5a0f2-149">Uruchom projekt do wysiewu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="5a0f2-150">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="5a0f2-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f2-152">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5a0f2-153">Wybierz **ASP.NET podstawową aplikację sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5a0f2-154">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="5a0f2-155">Ważne jest, aby użyć tej dokładnej nazwy, w tym wielkich liter, więc obszary nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="5a0f2-156">Wybierz pozycję **.NET Core** i **ASP.NET Core 3.0** w menu rozwijanym, a następnie wybierz pozycję **Aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5a0f2-158">W terminalu przejdź do folderu, w którym należy utworzyć folder projektu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="5a0f2-159">Uruchom następujące polecenia, aby utworzyć projekt `cd` Razor Pages i do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="5a0f2-160">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="5a0f2-160">Set up the site style</span></span>

<span data-ttu-id="5a0f2-161">Skonfiguruj nagłówek witryny, stopkę i menu, aktualizując *pages/shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="5a0f2-162">Zmień każde wystąpienie "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="5a0f2-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="5a0f2-163">Istnieją trzy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-163">There are three occurrences.</span></span>

* <span data-ttu-id="5a0f2-164">Usuń wpisy menu **Strona główna** i **Prywatność** oraz dodaj wpisy dla **informacji**, **Studenci**, **Kursy,** **Instruktorzy**i **Działy**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="5a0f2-165">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="5a0f2-166">W *Pages/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET Core tekstem o tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="5a0f2-167">Uruchom aplikację, aby sprawdzić, czy jest wyświetlana strona główna.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="5a0f2-168">Model danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-168">The data model</span></span>

<span data-ttu-id="5a0f2-169">Następujące sekcje tworzą model danych:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-169">The following sections create a data model:</span></span>

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

<span data-ttu-id="5a0f2-171">Uczeń może zapisać się na dowolną liczbę kursów, a kurs może mieć dowolną liczbę studentów.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="5a0f2-172">Jednostka Student</span><span class="sxs-lookup"><span data-stu-id="5a0f2-172">The Student entity</span></span>

![Diagram encji ucznia](intro/_static/student-entity.png)

* <span data-ttu-id="5a0f2-174">Utwórz folder *Modele* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="5a0f2-175">Utwórz *modele/student.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="5a0f2-176">Właściwość `ID` staje się kolumną klucza podstawowego tabeli bazy danych, która odpowiada tej klasie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="5a0f2-177">Domyślnie EF Core interpretuje właściwość, `ID` `classnameID` która jest nazwany lub jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="5a0f2-178">Tak więc alternatywą automatycznie `Student` rozpoznawaną nazwę `StudentID`dla klasy klucz podstawowy jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="5a0f2-179">Aby uzyskać więcej informacji, zobacz [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="5a0f2-180">Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="5a0f2-181">Właściwości nawigacji przechowują inne jednostki, które są powiązane z tą encją.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="5a0f2-182">W takim przypadku `Enrollments` właściwość `Student` jednostki posiada `Enrollment` wszystkie jednostki, które są związane z tym Student.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="5a0f2-183">Na przykład jeśli wiersz studenta w bazie danych ma `Enrollments` dwa powiązane wiersze rejestracji, właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="5a0f2-184">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora ucznia.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="5a0f2-185">Załóżmy na przykład, że wiersz studenta ma identyfikator=1.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="5a0f2-186">Powiązane wiersze rejestracji będą miały StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="5a0f2-187">StudentID jest *kluczem obcym* w tabeli Rejestracja.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="5a0f2-188">Właściwość `Enrollments` jest `ICollection<Enrollment>` zdefiniowana jako ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="5a0f2-189">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="5a0f2-190">Gdy `ICollection<Enrollment>` jest używany, EF `HashSet<Enrollment>` Core tworzy kolekcję domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="5a0f2-191">Jednostka Rejestracji</span><span class="sxs-lookup"><span data-stu-id="5a0f2-191">The Enrollment entity</span></span>

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="5a0f2-193">Utwórz *modele/enrollment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="5a0f2-194">Właściwość `EnrollmentID` jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca `ID` zamiast samego.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="5a0f2-195">W przypadku modelu danych produkcyjnych wybierz jeden wzorzec i użyj go konsekwentnie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="5a0f2-196">Ten samouczek używa zarówno tylko do zilustrowania, że oba działają.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="5a0f2-197">Korzystanie `ID` `classname` bez ułatwia implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="5a0f2-198">Obiekt `Grade` jest `enum`własnością .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="5a0f2-199">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="5a0f2-200">Stopień, który jest null różni się&mdash;od zerowej klasy null oznacza, że klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="5a0f2-201">Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="5a0f2-202">Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, `Student` więc właściwość zawiera jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="5a0f2-203">Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="5a0f2-204">Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="5a0f2-205">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwany `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="5a0f2-206">Na przykład`StudentID` jest kluczem `Student` obcym właściwości `Student` nawigacji, ponieważ kluczem podstawowym jednostki jest `ID`.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="5a0f2-207">Właściwości klucza obcego `<primary key property name>`mogą być również nazwane .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="5a0f2-208">Na przykład, `CourseID` `Course` ponieważ kluczem podstawowym `CourseID`jednostki jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="5a0f2-209">Jednostka Kurs</span><span class="sxs-lookup"><span data-stu-id="5a0f2-209">The Course entity</span></span>

![Diagram encji kursu](intro/_static/course-entity.png)

<span data-ttu-id="5a0f2-211">Utwórz *modele/course.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="5a0f2-212">Właściwość `Enrollments` jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="5a0f2-213">Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="5a0f2-214">Atrybut `DatabaseGenerated` umożliwia aplikacji określić klucz podstawowy, a nie o bazy danych wygenerować go.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="5a0f2-215">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="5a0f2-216">Strony dla uczniów rusztowania</span><span class="sxs-lookup"><span data-stu-id="5a0f2-216">Scaffold Student pages</span></span>

<span data-ttu-id="5a0f2-217">W tej sekcji służy ASP.NET narzędzie do tworzenia rusztowań rdzenia do generowania:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="5a0f2-218">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-218">An EF Core *context* class.</span></span> <span data-ttu-id="5a0f2-219">Kontekst jest klasą główną, która koordynuje funkcjonalność entity framework dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="5a0f2-220">Pochodzi z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="5a0f2-221">Strony maszynki do golenia obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` encji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f2-223">Tworzenie folderu *Uczniowie* w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="5a0f2-224">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Strony/Uczniowie* i wybierz pozycję **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5a0f2-225">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="5a0f2-226">W oknie dialogowym **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="5a0f2-227">Z **listy** rozwijanej Klasa Model wybierz pozycję **Uczeń (ContosoUniversity.Models).**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="5a0f2-228">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="5a0f2-229">Zmień nazwę kontekstu danych z *ContosoUniversity.Models.ContosoUniversityContext* na *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="5a0f2-230">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-230">Select **Add**.</span></span>

<span data-ttu-id="5a0f2-231">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5a0f2-233">Uruchom następujące polecenia .NET Core CLI, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="5a0f2-234">Pakiet Microsoft.VisualStudio.Web.CodeGeneration.Design jest wymagany do tworzenia rusztowań.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="5a0f2-235">Mimo że aplikacja nie będzie używać programu SQL Server, narzędzie do tworzenia szkieletów wymaga pakietu programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="5a0f2-236">Tworzenie folderu *Strony/Uczniowie.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="5a0f2-237">Uruchom następujące polecenie, aby zainstalować [narzędzie do tworzenia rusztowań aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="5a0f2-238">Uruchom następujące polecenie do tworzenia szkieletów stron uczniów.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="5a0f2-239">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="5a0f2-240">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="5a0f2-241">Jeśli masz problem z poprzednim krokiem, skompiluj projekt i ponów próbę kroku szkieletu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="5a0f2-242">Proces rusztowania:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-242">The scaffolding process:</span></span>

* <span data-ttu-id="5a0f2-243">Tworzy strony Razor w folderze *Strony/Uczniowie:*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="5a0f2-244">*Create.cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="5a0f2-245">*Usuń.cshtml* i *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="5a0f2-246">*Details.cshtml* i *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="5a0f2-247">*Edit.cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="5a0f2-248">*Index.cshtml* i *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="5a0f2-249">Tworzy *dane/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="5a0f2-250">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="5a0f2-251">Dodaje ciąg połączenia bazy danych do *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="5a0f2-252">Parametry połączenia bazy danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a0f2-254">Parametry połączenia określają [usługę SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="5a0f2-255">LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="5a0f2-256">Domyślnie LocalDB tworzy pliki *mdf* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a0f2-258">Zmień parametry połączenia, aby wskazać plik bazy danych SQLite o nazwie *CU.db:*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="5a0f2-259">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-259">Update the database context class</span></span>

<span data-ttu-id="5a0f2-260">Klasą główną, która koordynuje funkcje EF Core dla danego modelu danych jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="5a0f2-261">Kontekst pochodzi od pliku [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5a0f2-262">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="5a0f2-263">W tym projekcie klasa `SchoolContext`nosi nazwę .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="5a0f2-264">Zaktualizuj *SchoolContext.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="5a0f2-265">Wyróżniony kod tworzy [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwości dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="5a0f2-266">W terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-266">In EF Core terminology:</span></span>

* <span data-ttu-id="5a0f2-267">Zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="5a0f2-268">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5a0f2-269">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości DBSet powinny być nazwami mnogimi.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="5a0f2-270">Ponieważ narzędzie rusztowania`Student` utworzyło zestaw DBSet, ten `Students`krok zmienia go na mnogą .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="5a0f2-271">Aby kod Razor Pages był zgodny z nową nazwą DBSet, `_context.Student` dokonaj globalnej zmiany w całym projekcie na `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="5a0f2-272">Istnieje 8 zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-272">There are 8 occurrences.</span></span>

<span data-ttu-id="5a0f2-273">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="5a0f2-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="5a0f2-274">Startup.cs</span></span>

<span data-ttu-id="5a0f2-275">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5a0f2-276">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5a0f2-277">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5a0f2-278">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5a0f2-279">Narzędzie szkieletu automatycznie zarejestrował klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f2-281">W `ConfigureServices`, wyróżnione linie zostały dodane przez rusztowania:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5a0f2-283">W `ConfigureServices`, upewnij się, że kod dodany `UseSqlite`przez wywołanie rusztowania .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="5a0f2-284">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5a0f2-285">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="5a0f2-286">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-286">Create the database</span></span>

<span data-ttu-id="5a0f2-287">Zaktualizuj *Program.cs,* aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="5a0f2-288">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) Metoda nie wykonuje żadnych akcji, jeśli baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="5a0f2-289">Jeśli nie istnieje baza danych, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="5a0f2-290">`EnsureCreated`umożliwia następujące przepływ pracy do obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="5a0f2-291">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-291">Delete the database.</span></span> <span data-ttu-id="5a0f2-292">Wszystkie istniejące dane są tracone.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-292">Any existing data is lost.</span></span>
* <span data-ttu-id="5a0f2-293">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-293">Change the data model.</span></span> <span data-ttu-id="5a0f2-294">Na przykład dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="5a0f2-295">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-295">Run the app.</span></span>
* <span data-ttu-id="5a0f2-296">`EnsureCreated`tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="5a0f2-297">Ten przepływ pracy działa dobrze na wczesnym etapie rozwoju, gdy schemat szybko ewoluuje, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="5a0f2-298">Sytuacja jest inna, gdy dane, które zostały wprowadzone do bazy danych musi zostać zachowana.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="5a0f2-299">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="5a0f2-300">W dalszej części serii samouczków można `EnsureCreated` usunąć bazę danych, która została utworzona przez i zamiast tego używać migracji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="5a0f2-301">Nie można zaktualizować `EnsureCreated` bazy danych utworzonej przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="5a0f2-302">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="5a0f2-302">Test the app</span></span>

* <span data-ttu-id="5a0f2-303">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-303">Run the app.</span></span>
* <span data-ttu-id="5a0f2-304">Wybierz łącze **Uczniowie,** a następnie **utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="5a0f2-305">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="5a0f2-306">Seed bazy danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-306">Seed the database</span></span>

<span data-ttu-id="5a0f2-307">Metoda `EnsureCreated` tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="5a0f2-308">W tej sekcji dodano kod, który wypełnia bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="5a0f2-309">Utwórz *plik Data/DbInitializer.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-309">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="5a0f2-310">Kod sprawdza, czy w bazie danych są uczniowie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="5a0f2-311">Jeśli nie ma żadnych studentów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="5a0f2-312">Tworzy dane testowe w tablicach, `List<T>` a nie w kolekcjach w celu optymalizacji wydajności.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="5a0f2-313">W *Program.cs*, zastąp `EnsureCreated` `DbInitializer.Initialize` połączenie połączeniem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a0f2-315">Zatrzymaj aplikację, jeśli jest uruchomiona, i uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="5a0f2-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5a0f2-317">Zatrzymaj aplikację, jeśli jest uruchomiona, i usuń plik *CU.db.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="5a0f2-318">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-318">Restart the app.</span></span>

* <span data-ttu-id="5a0f2-319">Wybierz stronę Uczniowie, aby wyświetlić rozstawione dane.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="5a0f2-320">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f2-322">Otwórz **program SQL Server Object Explorer** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="5a0f2-323">W obszarze SSOX wybierz **(localdb)\MSSQLLocalDB > databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="5a0f2-324">Nazwa bazy danych jest generowana na podstawie nazwy kontekstu podanej wcześniej oraz myślnika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="5a0f2-325">Rozwiń węzeł **Tabele.**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="5a0f2-326">Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="5a0f2-327">Kliknij prawym przyciskiem myszy tabelę **Student** `Student` i kliknij `Student` polecenie **Wyświetl kod,** aby zobaczyć, jak model jest mapowany do schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a0f2-329">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane rozstawione.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="5a0f2-330">Plik bazy danych nosi nazwę *CU.db* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="5a0f2-331">Kod asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="5a0f2-331">Asynchronous code</span></span>

<span data-ttu-id="5a0f2-332">Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="5a0f2-333">Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="5a0f2-334">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="5a0f2-335">Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="5a0f2-336">Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="5a0f2-337">W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer może obsługiwać większy ruch bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="5a0f2-338">Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="5a0f2-339">W sytuacjach o niskim natężeniu ruchu osiągi są znikome, podczas gdy w przypadku sytuacji o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="5a0f2-340">W poniższym kodzie [asynchroniczne](/dotnet/csharp/language-reference/keywords/async) słowo kluczowe, `Task<T>` zwracana wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="5a0f2-341">Słowo `async` kluczowe mówi kompilatorowi:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="5a0f2-342">Generowanie wywołań zwrotnych dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="5a0f2-343">Utwórz obiekt [Zadanie,](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="5a0f2-344">Typ `Task<T>` zwracania reprezentuje bieżącą pracę.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="5a0f2-345">Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="5a0f2-346">Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="5a0f2-347">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="5a0f2-348">`ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="5a0f2-349">Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="5a0f2-350">Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="5a0f2-351">Obejmuje `ToListAsync`to `SingleOrDefaultAsync` `FirstOrDefaultAsync`, `SaveChangesAsync`, i .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="5a0f2-352">Nie zawiera instrukcji, które po `IQueryable`prostu `var students = context.Students.Where(s => s.LastName == "Davolio")`zmienić , takich jak .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="5a0f2-353">Kontekst EF Core nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="5a0f2-354">Aby skorzystać z zalet wydajności kodu asynchronii, sprawdź, czy pakiety biblioteki (takie jak do stronicowania) używają asynchronii, jeśli wywołują metody EF Core, które wysyłają zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="5a0f2-355">Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne za pomocą asynchronii i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5a0f2-356">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="5a0f2-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5a0f2-357">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="5a0f2-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5a0f2-358">Przykładowa aplikacja internetowa Contoso University pokazuje, jak utworzyć aplikację ASP.NET Core Razor Pages przy użyciu programu Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-358">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="5a0f2-359">Przykładowa aplikacja jest witryną sieci web fikcyjnego Uniwersytetu Contoso.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-359">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="5a0f2-360">Obejmuje funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-360">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="5a0f2-361">Ta strona jest pierwszą z serii samouczków, które wyjaśniają, jak utworzyć przykładową aplikację Uniwersytetu Contoso.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-361">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="5a0f2-362">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-362">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="5a0f2-363">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-363">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5a0f2-364">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5a0f2-364">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-365">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-366">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-366">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="5a0f2-367">Znajomość [stron Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-367">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="5a0f2-368">Nowi programiści powinni [ukończyć pracę z razor Pages](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-368">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5a0f2-369">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5a0f2-369">Troubleshooting</span></span>

<span data-ttu-id="5a0f2-370">Jeśli napotkasz problem, którego nie możesz rozwiązać, zazwyczaj możesz znaleźć rozwiązanie, porównując kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="5a0f2-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="5a0f2-371">Dobrym sposobem na uzyskanie pomocy jest opublikowanie pytania do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-371">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="5a0f2-372">Aplikacja internetowa Uniwersytetu Contoso</span><span class="sxs-lookup"><span data-stu-id="5a0f2-372">The Contoso University web app</span></span>

<span data-ttu-id="5a0f2-373">Aplikacja wbudowana w te samouczki jest podstawową stroną uniwersytecką.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-373">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="5a0f2-374">Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-374">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="5a0f2-375">Oto kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-375">Here are a few of the screens created in the tutorial.</span></span>

![Strona Indeks uczniów](intro/_static/students-index.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit.png)

<span data-ttu-id="5a0f2-378">Styl interfejsu użytkownika tej witryny jest zbliżony do tego, co jest generowane przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-378">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="5a0f2-379">Samouczek koncentruje się na EF Core ze stronami Razor, a nie interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-379">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="5a0f2-380">Tworzenie aplikacji internetowej Strony razorów ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="5a0f2-380">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-381">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f2-382">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-382">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5a0f2-383">Utwórz nową ASP.NET podstawową aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-383">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="5a0f2-384">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-384">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="5a0f2-385">Ważne jest, aby nadać nazwę projektowi *ContosoUniversity,* aby obszary nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-385">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="5a0f2-386">Wybierz **ASP.NET Core 2.1** w menu rozwijanym, a następnie wybierz pozycję **Aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-386">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="5a0f2-387">Aby zapoznać się z obrazami powyższych kroków, zobacz [Tworzenie aplikacji internetowej Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-387">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="5a0f2-388">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-388">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="5a0f2-390">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="5a0f2-390">Set up the site style</span></span>

<span data-ttu-id="5a0f2-391">Kilka zmian skonfigurować menu witryny, układ i stronę główną.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-391">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="5a0f2-392">Aktualizuj *strony/udostępnione/_Layout.cshtml* o następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-392">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="5a0f2-393">Zmień każde wystąpienie "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="5a0f2-393">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="5a0f2-394">Istnieją trzy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-394">There are three occurrences.</span></span>

* <span data-ttu-id="5a0f2-395">Dodaj pozycje menu dla **studentów,** **kursów,** **instruktorów**i **działów**i usuń wpis menu **Kontakt.**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-395">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="5a0f2-396">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-396">The changes are highlighted.</span></span> <span data-ttu-id="5a0f2-397">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-397">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="5a0f2-398">W *Pages/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET i MVC tekstem o tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-398">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="5a0f2-399">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-399">Create the data model</span></span>

<span data-ttu-id="5a0f2-400">Tworzenie klas jednostek dla aplikacji Contoso University.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-400">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="5a0f2-401">Zacznij od następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-401">Start with the following three entities:</span></span>

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

<span data-ttu-id="5a0f2-403">Istnieje relacja jeden do wielu między `Student` `Enrollment` jednostkami i jednostkami.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-403">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="5a0f2-404">Istnieje relacja jeden do wielu między `Course` `Enrollment` jednostkami i jednostkami.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-404">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="5a0f2-405">Student może zapisać się na dowolną liczbę kursów.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-405">A student can enroll in any number of courses.</span></span> <span data-ttu-id="5a0f2-406">Kurs może mieć dowolną liczbę studentów zapisanych w nim.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-406">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="5a0f2-407">W poniższych sekcjach tworzona jest klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-407">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="5a0f2-408">Jednostka Student</span><span class="sxs-lookup"><span data-stu-id="5a0f2-408">The Student entity</span></span>

![Diagram encji ucznia](intro/_static/student-entity.png)

<span data-ttu-id="5a0f2-410">Utwórz folder *Modele.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-410">Create a *Models* folder.</span></span> <span data-ttu-id="5a0f2-411">W folderze *Modele* utwórz plik klasy o nazwie *Student.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-411">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="5a0f2-412">Właściwość `ID` staje się kolumną klucza podstawowego tabeli bazy danych (DB), która odpowiada tej klasie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-412">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="5a0f2-413">Domyślnie EF Core interpretuje właściwość, `ID` `classnameID` która jest nazwany lub jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-413">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="5a0f2-414">W `classnameID` `classname` , jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-414">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="5a0f2-415">Alternatywny automatycznie rozpoznawany klucz `StudentID` podstawowy znajduje się w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-415">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="5a0f2-416">Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-416">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="5a0f2-417">Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą encją.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-417">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="5a0f2-418">W takim przypadku `Enrollments` właściwość `Student entity` posiada wszystkie `Enrollment` podmioty, które są `Student`z tym powiązane .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-418">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="5a0f2-419">Na przykład jeśli wiersz studenta w bazy danych ma `Enrollments` dwa powiązane wiersze rejestracji, właściwość nawigacji zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-419">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="5a0f2-420">Pokrewny `Enrollment` wiersz to wiersz zawierający podstawową `StudentID` wartość klucza ucznia w kolumnie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-420">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="5a0f2-421">Załóżmy na przykład, że uczeń z identyfikatorem=1 ma dwa wiersze w `Enrollment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-421">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="5a0f2-422">Tabela `Enrollment` ma dwa `StudentID` wiersze z = 1.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-422">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="5a0f2-423">`StudentID`jest kluczem obcym w `Enrollment` tabeli, `Student` który określa ucznia w tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-423">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="5a0f2-424">Jeśli właściwość nawigacji może pomieścić wiele jednostek, właściwość nawigacji `ICollection<T>`musi być typem listy, na przykład .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-424">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="5a0f2-425">`ICollection<T>`można określić lub typ, `List<T>` `HashSet<T>`taki jak lub .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-425">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="5a0f2-426">Gdy `ICollection<T>` jest używany, EF `HashSet<T>` Core tworzy kolekcję domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-426">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="5a0f2-427">Właściwości nawigacji, które przechowują wiele jednostek pochodzą z relacji wiele do wielu i jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-427">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="5a0f2-428">Jednostka Rejestracji</span><span class="sxs-lookup"><span data-stu-id="5a0f2-428">The Enrollment entity</span></span>

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="5a0f2-430">W folderze *Modele* utwórz *Enrollment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-430">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="5a0f2-431">Właściwość `EnrollmentID` jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-431">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="5a0f2-432">Ta jednostka używa `classnameID` wzorca, `ID` a `Student` nie jak jednostki.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-432">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="5a0f2-433">Zazwyczaj deweloperzy wybrać jeden wzorzec i używać go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-433">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="5a0f2-434">W późniejszym samouczku przy użyciu identyfikatora bez nazwy klasy jest wyświetlany, aby ułatwić implementowanie dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-434">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="5a0f2-435">Obiekt `Grade` jest `enum`własnością .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-435">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="5a0f2-436">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest nullable.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-436">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="5a0f2-437">Stopień, który ma wartość null, różni się od klasy zerowej — wartość null oznacza, że ocena nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-437">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="5a0f2-438">Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-438">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="5a0f2-439">Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, `Student` więc właściwość zawiera jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-439">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="5a0f2-440">Jednostka `Student` różni się `Student.Enrollments` od właściwości nawigacji, `Enrollment` która zawiera wiele jednostek.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-440">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="5a0f2-441">Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-441">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="5a0f2-442">Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-442">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="5a0f2-443">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwany `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-443">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="5a0f2-444">Na przykład`StudentID` dla `Student` właściwości nawigacji, `Student` ponieważ kluczem `ID`podstawowym jednostki jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-444">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="5a0f2-445">Właściwości klucza obcego `<primary key property name>`mogą być również nazwane .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-445">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="5a0f2-446">Na przykład, `CourseID` `Course` ponieważ kluczem podstawowym `CourseID`jednostki jest .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-446">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="5a0f2-447">Jednostka Kurs</span><span class="sxs-lookup"><span data-stu-id="5a0f2-447">The Course entity</span></span>

![Diagram encji kursu](intro/_static/course-entity.png)

<span data-ttu-id="5a0f2-449">W folderze *Modele* utwórz *Course.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-449">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="5a0f2-450">Właściwość `Enrollments` jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="5a0f2-451">Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="5a0f2-452">Atrybut `DatabaseGenerated` umożliwia aplikacji określić klucz podstawowy, a nie o db wygenerować go.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-452">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="5a0f2-453">Rusztowanie modelu studenta</span><span class="sxs-lookup"><span data-stu-id="5a0f2-453">Scaffold the student model</span></span>

<span data-ttu-id="5a0f2-454">W tej sekcji model studenta jest szkieletu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-454">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="5a0f2-455">Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-455">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="5a0f2-456">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-456">Build the project.</span></span>
* <span data-ttu-id="5a0f2-457">Utwórz folder *Strony/Uczniowie.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-457">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f2-459">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Strony/Uczniowie* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-459">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5a0f2-460">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-460">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="5a0f2-461">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-461">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5a0f2-462">Z **listy** rozwijanej Klasa Model wybierz pozycję **Uczeń (ContosoUniversity.Models).**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-462">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="5a0f2-463">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zmień nazwę wygenerowaną na **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-463">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="5a0f2-464">W **klasie kontekstu danych wybierz** pozycję **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-464">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="5a0f2-465">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-465">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="5a0f2-467">Zobacz [Szkielet modelu filmu,](xref:tutorials/razor-pages/model#scaffold-the-movie-model) jeśli masz problem z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-467">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-468">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-468">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a0f2-469">Uruchom następujące polecenia, aby s rusztowania modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-469">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="5a0f2-470">Proces rusztowania utworzył i zmienił następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-470">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5a0f2-471">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="5a0f2-471">Files created</span></span>

* <span data-ttu-id="5a0f2-472">*Strony/studenci* Tworzenie, usuwanie, szczegóły, edycja, indeks.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-472">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="5a0f2-473">*Dane/SzkołaContext.cs*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-473">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="5a0f2-474">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="5a0f2-474">File updates</span></span>

* <span data-ttu-id="5a0f2-475">*Startup.cs* : Zmiany w tym pliku są szczegółowo opisane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-475">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="5a0f2-476">*appsettings.json* : Ciąg połączenia używany do łączenia się z lokalną bazą danych jest dodawany.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-476">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5a0f2-477">Badanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="5a0f2-477">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5a0f2-478">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-478">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5a0f2-479">Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-479">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5a0f2-480">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-480">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5a0f2-481">Kod konstruktora, który pobiera wystąpienie kontekstu db jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-481">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5a0f2-482">Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-482">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5a0f2-483">Sprawdź `ConfigureServices` metodę w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-483">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="5a0f2-484">Podświetlona linia została dodana przez rusztowanie:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-484">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="5a0f2-485">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-485">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5a0f2-486">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-486">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="5a0f2-487">Aktualizacja główna</span><span class="sxs-lookup"><span data-stu-id="5a0f2-487">Update main</span></span>

<span data-ttu-id="5a0f2-488">W *Program.cs*zmodyfikuj metodę, `Main` aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-488">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="5a0f2-489">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-489">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="5a0f2-490">Wywołanie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-490">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="5a0f2-491">Zutylizuj `EnsureCreated` kontekst po zakończeniu metody.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-491">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="5a0f2-492">Poniższy kod przedstawia zaktualizowany plik *Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-492">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="5a0f2-493">`EnsureCreated`zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-493">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="5a0f2-494">Jeśli istnieje, nie zostaną podjęte żadne działania.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-494">If it exists, no action is taken.</span></span> <span data-ttu-id="5a0f2-495">Jeśli nie istnieje, a następnie bazy danych i wszystkie jego schematu są tworzone.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-495">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="5a0f2-496">`EnsureCreated`nie używa migracji do tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-496">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="5a0f2-497">Baza danych, która `EnsureCreated` jest tworzona z nie można później zaktualizować przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-497">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="5a0f2-498">`EnsureCreated`jest wywoływana na uruchomienie aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-498">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="5a0f2-499">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-499">Delete the DB.</span></span>
* <span data-ttu-id="5a0f2-500">Zmień schemat bazy danych (na przykład `EmailAddress` dodaj pole).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-500">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="5a0f2-501">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-501">Run the app.</span></span>
* <span data-ttu-id="5a0f2-502">`EnsureCreated`tworzy bazę danych`EmailAddress` z kolumną.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-502">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="5a0f2-503">`EnsureCreated`jest wygodne na wczesnym etapie rozwoju, gdy schemat szybko ewoluuje.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-503">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="5a0f2-504">W dalszej części samouczka baza danych jest usuwana i używane są migracje.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-504">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="5a0f2-505">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="5a0f2-505">Test the app</span></span>

<span data-ttu-id="5a0f2-506">Uruchom aplikację i zaakceptuj politykę plików cookie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-506">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="5a0f2-507">Ta aplikacja nie przechowuje danych osobowych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-507">This app doesn't keep personal information.</span></span> <span data-ttu-id="5a0f2-508">Informacje na temat polityki dotyczącej plików cookie można znaleźć na [stronie pomocy w ogólnym rozporządzeniu o ochronie danych (RODO).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="5a0f2-508">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="5a0f2-509">Wybierz łącze **Uczniowie,** a następnie **utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-509">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="5a0f2-510">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-510">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="5a0f2-511">Badanie kontekstu bazy danych SchoolContext</span><span class="sxs-lookup"><span data-stu-id="5a0f2-511">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="5a0f2-512">Klasą główną, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-512">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="5a0f2-513">Kontekst danych pochodzi z pliku [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-513">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5a0f2-514">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-514">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="5a0f2-515">W tym projekcie klasa `SchoolContext`nosi nazwę .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-515">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="5a0f2-516">Zaktualizuj *SchoolContext.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-516">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="5a0f2-517">Wyróżniony kod tworzy [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwości dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-517">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="5a0f2-518">W terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-518">In EF Core terminology:</span></span>

* <span data-ttu-id="5a0f2-519">Zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-519">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="5a0f2-520">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-520">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5a0f2-521">`DbSet<Enrollment>`i `DbSet<Course>` mogą zostać pominięte.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-521">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="5a0f2-522">EF Core zawiera je `Student` niejawnie, `Enrollment` ponieważ jednostka `Enrollment` odwołuje się `Course` do jednostki, a jednostka odwołuje się do jednostki.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-522">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="5a0f2-523">W tym samouczku, zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-523">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="5a0f2-524">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="5a0f2-524">SQL Server Express LocalDB</span></span>

<span data-ttu-id="5a0f2-525">Parametry połączenia określają [usługę SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-525">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="5a0f2-526">LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="5a0f2-527">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-527">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="5a0f2-528">Domyślnie LocalDB tworzy pliki *.mdf* `C:/Users/<user>` DB w katalogu.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-528">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="5a0f2-529">Dodaj kod, aby zainicjować bazę danych za pomocą danych testowych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-529">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="5a0f2-530">EF Core tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-530">EF Core creates an empty DB.</span></span> <span data-ttu-id="5a0f2-531">W tej sekcji `Initialize` metoda jest zapisywana, aby wypełnić go danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-531">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="5a0f2-532">W folderze *Dane* utwórz nowy plik klasy o nazwie *DbInitializer.cs* i dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-532">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="5a0f2-533">Uwaga: Poprzedni kod używa `Models` dla obszaru`namespace ContosoUniversity.Models`nazw ( `Data`) zamiast .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-533">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="5a0f2-534">`Models`jest zgodny z kodem wygenerowanym przez szkielety.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-534">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="5a0f2-535">Aby uzyskać więcej informacji, zobacz [ten problem z rusztowaniami GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-535">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="5a0f2-536">Kod sprawdza, czy w pb.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-536">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="5a0f2-537">Jeśli w db nie ma uczniów, baza danych jest inicjowana przy obliczu danych testowych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-537">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="5a0f2-538">Ładuje dane testowe do `List<T>` tablic, a nie kolekcji w celu optymalizacji wydajności.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-538">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="5a0f2-539">Metoda `EnsureCreated` automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-539">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="5a0f2-540">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-540">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="5a0f2-541">W *Program.cs*, zmodyfikuj `Main` metodę wywoływania: `Initialize`</span><span class="sxs-lookup"><span data-stu-id="5a0f2-541">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="5a0f2-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f2-542">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a0f2-543">Zatrzymaj aplikację, jeśli jest uruchomiona, i uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="5a0f2-543">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5a0f2-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f2-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5a0f2-545">Zatrzymaj aplikację, jeśli jest uruchomiona, i usuń plik *CU.db.*</span><span class="sxs-lookup"><span data-stu-id="5a0f2-545">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="5a0f2-546">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="5a0f2-546">View the DB</span></span>

<span data-ttu-id="5a0f2-547">Nazwa bazy danych jest generowana na podstawie nazwy kontekstu podanej wcześniej oraz myślnika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-547">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="5a0f2-548">W związku z tym nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="5a0f2-548">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="5a0f2-549">Identyfikator GUID będzie inny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-549">The GUID will be different for each user.</span></span>
<span data-ttu-id="5a0f2-550">Otwórz **program SQL Server Object Explorer** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-550">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="5a0f2-551">W obszarze SSOX kliknij **(localdb)\MSSQLLocalDB > databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-551">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="5a0f2-552">Rozwiń węzeł **Tabele.**</span><span class="sxs-lookup"><span data-stu-id="5a0f2-552">Expand the **Tables** node.</span></span>

<span data-ttu-id="5a0f2-553">Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-553">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="5a0f2-554">Kod asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="5a0f2-554">Asynchronous code</span></span>

<span data-ttu-id="5a0f2-555">Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-555">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="5a0f2-556">Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-556">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="5a0f2-557">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-557">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="5a0f2-558">Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-558">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="5a0f2-559">Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-559">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="5a0f2-560">W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer jest włączony do obsługi większej liczby ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-560">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="5a0f2-561">Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-561">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="5a0f2-562">W sytuacjach o niskim natężeniu ruchu osiągi są znikome, podczas gdy w przypadku sytuacji o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-562">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="5a0f2-563">W poniższym kodzie [asynchroniczne](/dotnet/csharp/language-reference/keywords/async) słowo kluczowe, `Task<T>` zwracana wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-563">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="5a0f2-564">Słowo `async` kluczowe mówi kompilatorowi:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-564">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="5a0f2-565">Generowanie wywołań zwrotnych dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-565">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="5a0f2-566">Automatycznie utwórz obiekt [Zadanie,](/dotnet/api/system.threading.tasks.task) który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-566">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="5a0f2-567">Aby uzyskać więcej informacji, zobacz [Typ zwrotu zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-567">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="5a0f2-568">Niejawny `Task` typ zwracany reprezentuje bieżącą pracę.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-568">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="5a0f2-569">Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-569">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="5a0f2-570">Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-570">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="5a0f2-571">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-571">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="5a0f2-572">`ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-572">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="5a0f2-573">Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="5a0f2-573">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="5a0f2-574">Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-574">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="5a0f2-575">Obejmuje to `ToListAsync` `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`, i .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-575">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="5a0f2-576">Nie zawiera instrukcji, które po `IQueryable`prostu `var students = context.Students.Where(s => s.LastName == "Davolio")`zmienić , takich jak .</span><span class="sxs-lookup"><span data-stu-id="5a0f2-576">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="5a0f2-577">Kontekst EF Core nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-577">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="5a0f2-578">Aby skorzystać z zalet wydajności kodu asynchronii, sprawdź, czy pakiety biblioteki (takie jak do stronicowania) używają asynchronii, jeśli wywołują metody EF Core, które wysyłają zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5a0f2-578">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="5a0f2-579">Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne za pomocą asynchronii i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-579">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="5a0f2-580">W następnym samouczku sprawdzane są podstawowe operacje CRUD (tworzenie, odczyt, aktualizacja, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="5a0f2-580">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="5a0f2-581">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5a0f2-581">Additional resources</span></span>

* [<span data-ttu-id="5a0f2-582">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="5a0f2-582">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="5a0f2-583">Dalej</span><span class="sxs-lookup"><span data-stu-id="5a0f2-583">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
