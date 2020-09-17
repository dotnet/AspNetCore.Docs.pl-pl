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
ms.openlocfilehash: 9dd8d293e189eebe6b61f6f0b35aee71977d2f77
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722556"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="425e3-103">Razor Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8</span><span class="sxs-lookup"><span data-stu-id="425e3-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="425e3-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="425e3-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="425e3-105">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="425e3-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="425e3-106">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="425e3-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="425e3-107">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="425e3-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="425e3-108">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="425e3-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="425e3-109">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="425e3-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="425e3-110">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="425e3-111">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="425e3-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="425e3-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="425e3-112">Prerequisites</span></span>

* <span data-ttu-id="425e3-113">Jeśli Razor dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do Razor stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="425e3-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="425e3-116">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-116">Database engines</span></span>

<span data-ttu-id="425e3-117">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="425e3-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="425e3-118">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="425e3-119">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="425e3-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="425e3-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="425e3-120">Troubleshooting</span></span>

<span data-ttu-id="425e3-121">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="425e3-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="425e3-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="425e3-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="425e3-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="425e3-123">The sample app</span></span>

<span data-ttu-id="425e3-124">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="425e3-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="425e3-125">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="425e3-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="425e3-126">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="425e3-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="425e3-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="425e3-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="425e3-130">Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="425e3-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="425e3-131">Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="425e3-132">Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="425e3-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="425e3-133">Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="425e3-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425e3-135">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="425e3-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="425e3-136">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="425e3-136">Build the project.</span></span>
* <span data-ttu-id="425e3-137">W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="425e3-137">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="425e3-138">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-138">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="425e3-140">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="425e3-140">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="425e3-141">Usuń *ContosoUniversity. csproj*i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="425e3-141">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="425e3-142">W *program.cs*, komentarz `#define Startup` `StartupSQLite` jest używany.</span><span class="sxs-lookup"><span data-stu-id="425e3-142">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="425e3-143">Usuń *appSettings.js*i zmień nazwę *appSettingsSQLite.jsna* na *appSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="425e3-143">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="425e3-144">Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.</span><span class="sxs-lookup"><span data-stu-id="425e3-144">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="425e3-145">Wykonaj wyszukiwanie globalne dla `#if SQLiteVersion` i Usuń `#if SQLiteVersion` oraz skojarzoną `#endif` instrukcję.</span><span class="sxs-lookup"><span data-stu-id="425e3-145">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="425e3-146">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="425e3-146">Build the project.</span></span>
* <span data-ttu-id="425e3-147">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="425e3-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="425e3-148">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="425e3-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="425e3-149">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="425e3-150">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="425e3-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-152">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="425e3-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="425e3-153">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="425e3-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="425e3-154">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="425e3-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="425e3-155">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="425e3-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="425e3-156">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="425e3-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-158">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="425e3-159">Uruchom następujące polecenia, aby utworzyć Razor Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="425e3-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="425e3-160">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="425e3-160">Set up the site style</span></span>

<span data-ttu-id="425e3-161">Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="425e3-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="425e3-162">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="425e3-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="425e3-163">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="425e3-163">There are three occurrences.</span></span>

* <span data-ttu-id="425e3-164">Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów**, **kursów**, **instruktorów**i **działów**.</span><span class="sxs-lookup"><span data-stu-id="425e3-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="425e3-165">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="425e3-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="425e3-166">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="425e3-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="425e3-167">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="425e3-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="425e3-168">Model danych</span><span class="sxs-lookup"><span data-stu-id="425e3-168">The data model</span></span>

<span data-ttu-id="425e3-169">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="425e3-169">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="425e3-171">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="425e3-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="425e3-172">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="425e3-172">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="425e3-174">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="425e3-175">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="425e3-176">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="425e3-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="425e3-177">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="425e3-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="425e3-178">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="425e3-179">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="425e3-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="425e3-180">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="425e3-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="425e3-181">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="425e3-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="425e3-182">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="425e3-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="425e3-183">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="425e3-184">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="425e3-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="425e3-185">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="425e3-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="425e3-186">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="425e3-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="425e3-187">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="425e3-188">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="425e3-189">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="425e3-190">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="425e3-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="425e3-191">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="425e3-191">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="425e3-193">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="425e3-194">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="425e3-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="425e3-195">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="425e3-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="425e3-196">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="425e3-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="425e3-197">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="425e3-198">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="425e3-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="425e3-199">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="425e3-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="425e3-200">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="425e3-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="425e3-201">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="425e3-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="425e3-202">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="425e3-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="425e3-203">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="425e3-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="425e3-204">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="425e3-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="425e3-205">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="425e3-206">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="425e3-207">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="425e3-208">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="425e3-209">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="425e3-209">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="425e3-211">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="425e3-212">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="425e3-213">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="425e3-214">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="425e3-215">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="425e3-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="425e3-216">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="425e3-216">Scaffold Student pages</span></span>

<span data-ttu-id="425e3-217">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="425e3-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="425e3-218">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="425e3-218">An EF Core *context* class.</span></span> <span data-ttu-id="425e3-219">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="425e3-220">Pochodzi od `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="425e3-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="425e3-221">Razor strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="425e3-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-223">Utwórz folder *uczniów* w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="425e3-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="425e3-224">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="425e3-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="425e3-225">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="425e3-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="425e3-226">Na **stronie Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="425e3-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="425e3-227">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="425e3-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="425e3-228">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="425e3-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="425e3-229">Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="425e3-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="425e3-230">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="425e3-230">Select **Add**.</span></span>

<span data-ttu-id="425e3-231">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="425e3-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-233">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="425e3-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="425e3-234">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="425e3-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="425e3-235">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="425e3-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="425e3-236">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="425e3-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="425e3-237">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="425e3-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="425e3-238">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="425e3-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="425e3-239">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="425e3-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="425e3-240">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="425e3-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="425e3-241">Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="425e3-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="425e3-242">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="425e3-242">The scaffolding process:</span></span>

* <span data-ttu-id="425e3-243">Tworzy Razor strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="425e3-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="425e3-244">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-245">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-246">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-247">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-248">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="425e3-249">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="425e3-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="425e3-250">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="425e3-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="425e3-251">Dodaje parametry połączenia z bazą danych do *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="425e3-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="425e3-252">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="425e3-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425e3-254">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="425e3-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="425e3-255">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="425e3-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="425e3-256">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="425e3-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="425e3-258">Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="425e3-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="425e3-259">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-259">Update the database context class</span></span>

<span data-ttu-id="425e3-260">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="425e3-261">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="425e3-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="425e3-262">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="425e3-263">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="425e3-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="425e3-264">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="425e3-265">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="425e3-266">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="425e3-266">In EF Core terminology:</span></span>

* <span data-ttu-id="425e3-267">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="425e3-268">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="425e3-269">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="425e3-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="425e3-270">Ponieważ narzędzie tworzenia szkieletów utworzyło `Student` nieogólnymi, ten krok zmienia go na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="425e3-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="425e3-271">Aby Razor kod stron był zgodny z nową nazwą nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="425e3-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="425e3-272">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="425e3-272">There are 8 occurrences.</span></span>

<span data-ttu-id="425e3-273">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="425e3-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="425e3-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="425e3-274">Startup.cs</span></span>

<span data-ttu-id="425e3-275">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="425e3-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="425e3-276">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="425e3-277">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="425e3-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="425e3-278">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="425e3-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="425e3-279">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="425e3-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-281">W programie `ConfigureServices` wyróżnione wiersze zostały dodane przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="425e3-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-283">W programie upewnij `ConfigureServices` się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="425e3-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="425e3-284">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="425e3-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="425e3-285">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="425e3-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="425e3-286">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-286">Create the database</span></span>

<span data-ttu-id="425e3-287">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="425e3-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="425e3-288">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="425e3-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="425e3-289">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="425e3-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="425e3-290">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="425e3-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="425e3-291">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-291">Delete the database.</span></span> <span data-ttu-id="425e3-292">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="425e3-292">Any existing data is lost.</span></span>
* <span data-ttu-id="425e3-293">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-293">Change the data model.</span></span> <span data-ttu-id="425e3-294">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="425e3-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="425e3-295">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-295">Run the app.</span></span>
* <span data-ttu-id="425e3-296">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="425e3-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="425e3-297">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="425e3-298">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="425e3-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="425e3-299">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="425e3-300">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="425e3-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="425e3-301">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="425e3-302">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="425e3-302">Test the app</span></span>

* <span data-ttu-id="425e3-303">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-303">Run the app.</span></span>
* <span data-ttu-id="425e3-304">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="425e3-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="425e3-305">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="425e3-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="425e3-306">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-306">Seed the database</span></span>

<span data-ttu-id="425e3-307">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="425e3-308">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="425e3-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="425e3-309">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-309">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="425e3-310">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="425e3-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="425e3-311">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="425e3-312">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="425e3-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="425e3-313">W *program.cs*Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="425e3-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425e3-315">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="425e3-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-317">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="425e3-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="425e3-318">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-318">Restart the app.</span></span>

* <span data-ttu-id="425e3-319">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="425e3-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="425e3-320">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-322">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="425e3-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="425e3-323">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="425e3-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="425e3-324">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="425e3-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="425e3-325">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="425e3-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="425e3-326">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="425e3-327">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="425e3-329">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="425e3-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="425e3-330">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="425e3-331">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="425e3-331">Asynchronous code</span></span>

<span data-ttu-id="425e3-332">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="425e3-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="425e3-333">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="425e3-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="425e3-334">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="425e3-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="425e3-335">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="425e3-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="425e3-336">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="425e3-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="425e3-337">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="425e3-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="425e3-338">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="425e3-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="425e3-339">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="425e3-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="425e3-340">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="425e3-341">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="425e3-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="425e3-342">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="425e3-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="425e3-343">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="425e3-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="425e3-344">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="425e3-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="425e3-345">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="425e3-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="425e3-346">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="425e3-347">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="425e3-348">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="425e3-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="425e3-349">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="425e3-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="425e3-350">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="425e3-351">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="425e3-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="425e3-352">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="425e3-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="425e3-353">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="425e3-354">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="425e3-355">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="425e3-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="425e3-356">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="425e3-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="425e3-357">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="425e3-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="425e3-358">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="425e3-358">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="425e3-359">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="425e3-359">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="425e3-360">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="425e3-360">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="425e3-361">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="425e3-361">The tutorial uses the code first approach.</span></span> <span data-ttu-id="425e3-362">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="425e3-362">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="425e3-363">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-363">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="425e3-364">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="425e3-364">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="425e3-365">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="425e3-365">Prerequisites</span></span>

* <span data-ttu-id="425e3-366">Jeśli Razor dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do Razor stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="425e3-366">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-367">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-367">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-368">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-368">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="425e3-369">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-369">Database engines</span></span>

<span data-ttu-id="425e3-370">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="425e3-370">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="425e3-371">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-371">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="425e3-372">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="425e3-372">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="425e3-373">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="425e3-373">Troubleshooting</span></span>

<span data-ttu-id="425e3-374">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="425e3-374">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="425e3-375">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="425e3-375">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="425e3-376">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="425e3-376">The sample app</span></span>

<span data-ttu-id="425e3-377">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="425e3-377">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="425e3-378">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="425e3-378">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="425e3-379">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="425e3-379">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="425e3-382">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="425e3-382">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="425e3-383">Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="425e3-383">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="425e3-384">Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-384">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="425e3-385">Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="425e3-385">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="425e3-386">Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="425e3-386">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-387">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425e3-388">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="425e3-388">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="425e3-389">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="425e3-389">Build the project.</span></span>
* <span data-ttu-id="425e3-390">W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="425e3-390">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="425e3-391">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-391">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-392">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-392">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="425e3-393">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="425e3-393">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="425e3-394">Usuń *ContosoUniversity. csproj*i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="425e3-394">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="425e3-395">W *program.cs*, komentarz `#define Startup` `StartupSQLite` jest używany.</span><span class="sxs-lookup"><span data-stu-id="425e3-395">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="425e3-396">Usuń *appSettings.js*i zmień nazwę *appSettingsSQLite.jsna* na *appSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="425e3-396">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="425e3-397">Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.</span><span class="sxs-lookup"><span data-stu-id="425e3-397">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="425e3-398">Wykonaj wyszukiwanie globalne dla `#if SQLiteVersion` i Usuń `#if SQLiteVersion` oraz skojarzoną `#endif` instrukcję.</span><span class="sxs-lookup"><span data-stu-id="425e3-398">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="425e3-399">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="425e3-399">Build the project.</span></span>
* <span data-ttu-id="425e3-400">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="425e3-400">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="425e3-401">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="425e3-401">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="425e3-402">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-402">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="425e3-403">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="425e3-403">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-404">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-405">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="425e3-405">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="425e3-406">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="425e3-406">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="425e3-407">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="425e3-407">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="425e3-408">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="425e3-408">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="425e3-409">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="425e3-409">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-411">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-411">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="425e3-412">Uruchom następujące polecenia, aby utworzyć Razor Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="425e3-412">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="425e3-413">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="425e3-413">Set up the site style</span></span>

<span data-ttu-id="425e3-414">Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="425e3-414">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="425e3-415">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="425e3-415">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="425e3-416">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="425e3-416">There are three occurrences.</span></span>

* <span data-ttu-id="425e3-417">Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów**, **kursów**, **instruktorów**i **działów**.</span><span class="sxs-lookup"><span data-stu-id="425e3-417">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="425e3-418">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="425e3-418">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="425e3-419">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="425e3-419">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="425e3-420">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="425e3-420">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="425e3-421">Model danych</span><span class="sxs-lookup"><span data-stu-id="425e3-421">The data model</span></span>

<span data-ttu-id="425e3-422">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="425e3-422">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="425e3-424">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="425e3-424">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="425e3-425">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="425e3-425">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="425e3-427">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-427">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="425e3-428">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-428">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="425e3-429">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="425e3-429">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="425e3-430">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="425e3-430">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="425e3-431">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-431">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="425e3-432">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="425e3-432">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="425e3-433">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="425e3-433">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="425e3-434">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="425e3-434">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="425e3-435">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="425e3-435">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="425e3-436">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-436">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="425e3-437">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="425e3-437">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="425e3-438">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="425e3-438">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="425e3-439">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="425e3-439">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="425e3-440">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-440">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="425e3-441">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-441">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="425e3-442">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-442">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="425e3-443">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="425e3-443">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="425e3-444">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="425e3-444">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="425e3-446">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-446">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="425e3-447">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="425e3-447">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="425e3-448">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="425e3-448">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="425e3-449">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="425e3-449">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="425e3-450">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-450">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="425e3-451">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="425e3-451">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="425e3-452">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="425e3-452">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="425e3-453">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="425e3-453">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="425e3-454">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="425e3-454">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="425e3-455">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="425e3-455">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="425e3-456">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="425e3-456">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="425e3-457">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="425e3-457">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="425e3-458">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-458">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="425e3-459">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-459">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="425e3-460">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-460">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="425e3-461">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-461">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="425e3-462">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="425e3-462">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="425e3-464">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-464">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="425e3-465">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-465">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="425e3-466">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-466">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="425e3-467">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-467">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="425e3-468">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="425e3-468">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="425e3-469">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="425e3-469">Scaffold Student pages</span></span>

<span data-ttu-id="425e3-470">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="425e3-470">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="425e3-471">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="425e3-471">An EF Core *context* class.</span></span> <span data-ttu-id="425e3-472">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-472">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="425e3-473">Pochodzi od `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="425e3-473">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="425e3-474">Razor strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="425e3-474">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-475">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-476">Utwórz folder *uczniów* w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="425e3-476">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="425e3-477">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="425e3-477">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="425e3-478">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="425e3-478">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="425e3-479">Na **stronie Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="425e3-479">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="425e3-480">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="425e3-480">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="425e3-481">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="425e3-481">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="425e3-482">Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="425e3-482">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="425e3-483">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="425e3-483">Select **Add**.</span></span>

<span data-ttu-id="425e3-484">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="425e3-484">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-485">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-485">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-486">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="425e3-486">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="425e3-487">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="425e3-487">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="425e3-488">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="425e3-488">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="425e3-489">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="425e3-489">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="425e3-490">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="425e3-490">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="425e3-491">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="425e3-491">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="425e3-492">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="425e3-492">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="425e3-493">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="425e3-493">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="425e3-494">Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="425e3-494">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="425e3-495">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="425e3-495">The scaffolding process:</span></span>

* <span data-ttu-id="425e3-496">Tworzy Razor strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="425e3-496">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="425e3-497">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-497">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-498">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-498">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-499">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-499">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-500">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-500">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="425e3-501">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-501">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="425e3-502">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="425e3-502">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="425e3-503">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="425e3-503">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="425e3-504">Dodaje parametry połączenia z bazą danych do *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="425e3-504">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="425e3-505">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="425e3-505">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-506">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425e3-507">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="425e3-507">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="425e3-508">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="425e3-508">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="425e3-509">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="425e3-509">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-510">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-510">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="425e3-511">Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="425e3-511">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="425e3-512">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-512">Update the database context class</span></span>

<span data-ttu-id="425e3-513">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-513">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="425e3-514">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="425e3-514">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="425e3-515">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-515">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="425e3-516">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="425e3-516">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="425e3-517">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-517">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="425e3-518">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-518">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="425e3-519">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="425e3-519">In EF Core terminology:</span></span>

* <span data-ttu-id="425e3-520">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-520">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="425e3-521">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-521">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="425e3-522">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="425e3-522">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="425e3-523">Ponieważ narzędzie tworzenia szkieletów utworzyło `Student` nieogólnymi, ten krok zmienia go na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="425e3-523">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="425e3-524">Aby Razor kod stron był zgodny z nową nazwą nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="425e3-524">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="425e3-525">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="425e3-525">There are 8 occurrences.</span></span>

<span data-ttu-id="425e3-526">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="425e3-526">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="425e3-527">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="425e3-527">Startup.cs</span></span>

<span data-ttu-id="425e3-528">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="425e3-528">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="425e3-529">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-529">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="425e3-530">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="425e3-530">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="425e3-531">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="425e3-531">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="425e3-532">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="425e3-532">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-533">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-534">W programie `ConfigureServices` wyróżnione wiersze zostały dodane przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="425e3-534">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-535">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-535">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-536">W programie upewnij `ConfigureServices` się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="425e3-536">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="425e3-537">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="425e3-537">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="425e3-538">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="425e3-538">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="425e3-539">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-539">Create the database</span></span>

<span data-ttu-id="425e3-540">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="425e3-540">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="425e3-541">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="425e3-541">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="425e3-542">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="425e3-542">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="425e3-543">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="425e3-543">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="425e3-544">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-544">Delete the database.</span></span> <span data-ttu-id="425e3-545">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="425e3-545">Any existing data is lost.</span></span>
* <span data-ttu-id="425e3-546">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-546">Change the data model.</span></span> <span data-ttu-id="425e3-547">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="425e3-547">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="425e3-548">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-548">Run the app.</span></span>
* <span data-ttu-id="425e3-549">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="425e3-549">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="425e3-550">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-550">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="425e3-551">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="425e3-551">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="425e3-552">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-552">When that is the case, use migrations.</span></span>

<span data-ttu-id="425e3-553">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="425e3-553">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="425e3-554">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-554">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="425e3-555">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="425e3-555">Test the app</span></span>

* <span data-ttu-id="425e3-556">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-556">Run the app.</span></span>
* <span data-ttu-id="425e3-557">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="425e3-557">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="425e3-558">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="425e3-558">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="425e3-559">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-559">Seed the database</span></span>

<span data-ttu-id="425e3-560">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-560">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="425e3-561">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="425e3-561">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="425e3-562">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-562">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="425e3-563">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="425e3-563">The code checks if there are any students in the database.</span></span> <span data-ttu-id="425e3-564">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-564">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="425e3-565">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="425e3-565">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="425e3-566">W *program.cs*Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="425e3-566">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-567">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-567">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425e3-568">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="425e3-568">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-569">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-569">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-570">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="425e3-570">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="425e3-571">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-571">Restart the app.</span></span>

* <span data-ttu-id="425e3-572">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="425e3-572">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="425e3-573">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-573">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-574">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-574">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-575">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="425e3-575">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="425e3-576">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="425e3-576">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="425e3-577">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="425e3-577">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="425e3-578">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="425e3-578">Expand the **Tables** node.</span></span>
* <span data-ttu-id="425e3-579">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-579">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="425e3-580">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-580">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="425e3-582">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="425e3-582">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="425e3-583">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-583">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="425e3-584">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="425e3-584">Asynchronous code</span></span>

<span data-ttu-id="425e3-585">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="425e3-585">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="425e3-586">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="425e3-586">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="425e3-587">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="425e3-587">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="425e3-588">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="425e3-588">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="425e3-589">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="425e3-589">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="425e3-590">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="425e3-590">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="425e3-591">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="425e3-591">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="425e3-592">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="425e3-592">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="425e3-593">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-593">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="425e3-594">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="425e3-594">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="425e3-595">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="425e3-595">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="425e3-596">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="425e3-596">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="425e3-597">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="425e3-597">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="425e3-598">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="425e3-598">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="425e3-599">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-599">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="425e3-600">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-600">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="425e3-601">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="425e3-601">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="425e3-602">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="425e3-602">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="425e3-603">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-603">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="425e3-604">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="425e3-604">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="425e3-605">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="425e3-605">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="425e3-606">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-606">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="425e3-607">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-607">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="425e3-608">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="425e3-608">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="425e3-609">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="425e3-609">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="425e3-610">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="425e3-610">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="425e3-611">Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji ASP.NET Core Razor stronach przy użyciu programu Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="425e3-611">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="425e3-612">Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="425e3-612">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="425e3-613">Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="425e3-613">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="425e3-614">Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają sposób tworzenia przykładowej aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="425e3-614">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="425e3-615">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-615">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="425e3-616">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="425e3-616">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="425e3-617">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="425e3-617">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-618">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-618">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-619">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-619">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="425e3-620">Znajomość [ Razor stron](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="425e3-620">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="425e3-621">Nowi programiści powinni zakończyć pracę [z Razor stronami](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="425e3-621">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="425e3-622">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="425e3-622">Troubleshooting</span></span>

<span data-ttu-id="425e3-623">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="425e3-623">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="425e3-624">Dobrym sposobem uzyskania pomocy jest zaksięgowanie pytania [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) na [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="425e3-624">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="425e3-625">Aplikacja sieci Web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="425e3-625">The Contoso University web app</span></span>

<span data-ttu-id="425e3-626">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="425e3-626">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="425e3-627">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="425e3-627">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="425e3-628">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="425e3-628">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

<span data-ttu-id="425e3-631">Styl interfejsu użytkownika tej witryny jest zbliżony do zawartości wygenerowanej przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="425e3-631">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="425e3-632">Fokus samouczka znajduje się na EF Core ze Razor stronami, a nie interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="425e3-632">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="425e3-633">Tworzenie Razor aplikacji sieci Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="425e3-633">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-634">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-634">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-635">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="425e3-635">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="425e3-636">Utwórz nową aplikację sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="425e3-636">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="425e3-637">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="425e3-637">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="425e3-638">Ważne jest, aby nazwa projektu *ContosoUniversity* , tak aby przestrzenie nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="425e3-638">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="425e3-639">Wybierz pozycję **ASP.NET Core 2,1** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="425e3-639">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="425e3-640">Aby poznać obrazy powyższych kroków, zobacz [Tworzenie Razor aplikacji sieci Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="425e3-640">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="425e3-641">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-641">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-642">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-642">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="425e3-643">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="425e3-643">Set up the site style</span></span>

<span data-ttu-id="425e3-644">Kilka zmian powoduje skonfigurowanie menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="425e3-644">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="425e3-645">Aktualizowanie *stron/Shared/_Layout. cshtml* z następującymi zmianami:</span><span class="sxs-lookup"><span data-stu-id="425e3-645">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="425e3-646">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="425e3-646">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="425e3-647">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="425e3-647">There are three occurrences.</span></span>

* <span data-ttu-id="425e3-648">Dodaj pozycje menu dla **studentów**, **kursów**, **instruktorów**i **działów**, a następnie usuń wpis menu **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="425e3-648">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="425e3-649">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="425e3-649">The changes are highlighted.</span></span> <span data-ttu-id="425e3-650">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="425e3-650">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="425e3-651">W obszarze *Pages/index. cshtml*Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="425e3-651">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="425e3-652">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="425e3-652">Create the data model</span></span>

<span data-ttu-id="425e3-653">Utwórz klasy jednostek dla aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="425e3-653">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="425e3-654">Zacznij od następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="425e3-654">Start with the following three entities:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="425e3-656">Istnieje relacja jeden do wielu między elementami `Student` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="425e3-656">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="425e3-657">Istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="425e3-657">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="425e3-658">Student może zarejestrować się w dowolnej liczbie kursów.</span><span class="sxs-lookup"><span data-stu-id="425e3-658">A student can enroll in any number of courses.</span></span> <span data-ttu-id="425e3-659">Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.</span><span class="sxs-lookup"><span data-stu-id="425e3-659">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="425e3-660">W poniższych sekcjach zostanie utworzona Klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-660">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="425e3-661">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="425e3-661">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

<span data-ttu-id="425e3-663">Utwórz folder *models* .</span><span class="sxs-lookup"><span data-stu-id="425e3-663">Create a *Models* folder.</span></span> <span data-ttu-id="425e3-664">W folderze *modele* Utwórz plik klasy o nazwie *student.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-664">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="425e3-665">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych (DB), która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="425e3-665">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="425e3-666">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="425e3-666">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="425e3-667">W `classnameID` , `classname` jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="425e3-667">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="425e3-668">Alternatywny automatycznie rozpoznany klucz podstawowy znajduje się `StudentID` w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="425e3-668">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="425e3-669">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="425e3-669">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="425e3-670">Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="425e3-670">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="425e3-671">W tym przypadku `Enrollments` Właściwość a `Student entity` zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym `Student` .</span><span class="sxs-lookup"><span data-stu-id="425e3-671">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="425e3-672">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="425e3-672">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="425e3-673">Powiązany `Enrollment` wiersz jest wierszem zawierającym wartość klucza podstawowego studenta w `StudentID` kolumnie.</span><span class="sxs-lookup"><span data-stu-id="425e3-673">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="425e3-674">Załóżmy na przykład, że student o IDENTYFIKATORze 1 ma dwa wiersze w `Enrollment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-674">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="425e3-675">`Enrollment`Tabela ma dwa wiersze z `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="425e3-675">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="425e3-676">`StudentID` jest kluczem obcym w `Enrollment` tabeli, który określa student w `Student` tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-676">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="425e3-677">Jeśli właściwość nawigacji może zawierać wiele jednostek, właściwość nawigacji musi być typem listy, na przykład `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-677">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="425e3-678">`ICollection<T>` można określić typ, taki jak `List<T>` lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-678">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="425e3-679">Gdy `ICollection<T>` jest używany, EF Core domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="425e3-679">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="425e3-680">Właściwości nawigacji, które przechowują wiele jednostek, pochodzą z relacji "wiele do wielu" i "jeden do wielu".</span><span class="sxs-lookup"><span data-stu-id="425e3-680">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="425e3-681">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="425e3-681">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="425e3-683">W folderze *modele* Utwórz *Enrollment.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-683">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="425e3-684">`EnrollmentID`Właściwość jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="425e3-684">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="425e3-685">Ta jednostka używa `classnameID` wzorca zamiast `ID` `Student` obiektu.</span><span class="sxs-lookup"><span data-stu-id="425e3-685">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="425e3-686">Zazwyczaj deweloperzy wybierają jeden wzorzec i używają go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-686">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="425e3-687">W późniejszym samouczku, użycie identyfikatora bez ClassName jest pokazane, aby ułatwić implementację dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-687">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="425e3-688">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="425e3-688">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="425e3-689">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null.</span><span class="sxs-lookup"><span data-stu-id="425e3-689">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="425e3-690">Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="425e3-690">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="425e3-691">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="425e3-691">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="425e3-692">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="425e3-692">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="425e3-693">`Student`Jednostka różni się od `Student.Enrollments` właściwości nawigacji, która zawiera wiele `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-693">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="425e3-694">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="425e3-694">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="425e3-695">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="425e3-695">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="425e3-696">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-696">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="425e3-697">Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-697">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="425e3-698">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="425e3-698">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="425e3-699">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="425e3-699">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="425e3-700">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="425e3-700">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="425e3-702">W folderze *modele* Utwórz *Course.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-702">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="425e3-703">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-703">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="425e3-704">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-704">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="425e3-705">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast generowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-705">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="425e3-706">Tworzenie szkieletu modelu ucznia</span><span class="sxs-lookup"><span data-stu-id="425e3-706">Scaffold the student model</span></span>

<span data-ttu-id="425e3-707">W tej sekcji model ucznia jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="425e3-707">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="425e3-708">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="425e3-708">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="425e3-709">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="425e3-709">Build the project.</span></span>
* <span data-ttu-id="425e3-710">Utwórz folder *strony/uczniowie* .</span><span class="sxs-lookup"><span data-stu-id="425e3-710">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-711">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-711">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="425e3-712">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder *strony/uczniowie* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="425e3-712">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="425e3-713">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="425e3-713">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="425e3-714">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="425e3-714">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="425e3-715">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="425e3-715">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="425e3-716">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę na **ContosoUniversity. models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="425e3-716">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="425e3-717">Z listy rozwijanej **Klasa kontekstu danych** wybierz pozycję **ContosoUniversity. models. SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="425e3-717">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="425e3-718">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="425e3-718">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="425e3-720">Zobacz Tworzenie [szkieletu modelu filmu w](xref:tutorials/razor-pages/model#scaffold-the-movie-model) przypadku problemu z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="425e3-720">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-721">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-721">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="425e3-722">Uruchom następujące polecenia, aby uzyskać szkielet modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="425e3-722">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="425e3-723">Proces szkieletu utworzył i zmienił następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="425e3-723">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="425e3-724">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="425e3-724">Files created</span></span>

* <span data-ttu-id="425e3-725">*Strony/uczniowie* Tworzenie, usuwanie, szczegóły, edytowanie, indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="425e3-725">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="425e3-726">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="425e3-726">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="425e3-727">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="425e3-727">File updates</span></span>

* <span data-ttu-id="425e3-728">*Startup.cs* : szczegółowe zmiany w tym pliku opisano w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="425e3-728">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="425e3-729">*appsettings.json* : dodano parametry połączenia używane do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-729">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="425e3-730">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="425e3-730">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="425e3-731">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="425e3-731">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="425e3-732">Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-732">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="425e3-733">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="425e3-733">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="425e3-734">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="425e3-734">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="425e3-735">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="425e3-735">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="425e3-736">Zapoznaj się z `ConfigureServices` metodą w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="425e3-736">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="425e3-737">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="425e3-737">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="425e3-738">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="425e3-738">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="425e3-739">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="425e3-739">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="425e3-740">Aktualizuj główne</span><span class="sxs-lookup"><span data-stu-id="425e3-740">Update main</span></span>

<span data-ttu-id="425e3-741">W *program.cs*Zmień metodę, `Main` Aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="425e3-741">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="425e3-742">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="425e3-742">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="425e3-743">Wywołaj  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="425e3-743">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="425e3-744">Usuń kontekst, gdy `EnsureCreated` Metoda zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="425e3-744">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="425e3-745">Poniższy kod przedstawia zaktualizowany plik *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="425e3-745">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="425e3-746">`EnsureCreated` zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="425e3-746">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="425e3-747">Jeśli istnieje, nie zostanie podjęta żadna akcja.</span><span class="sxs-lookup"><span data-stu-id="425e3-747">If it exists, no action is taken.</span></span> <span data-ttu-id="425e3-748">Jeśli nie istnieje, zostanie utworzona baza danych i jej wszystkie schematy.</span><span class="sxs-lookup"><span data-stu-id="425e3-748">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="425e3-749">`EnsureCreated` Program nie korzysta z migracji w celu utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-749">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="425e3-750">Baza danych utworzona za pomocą programu `EnsureCreated` nie może zostać zaktualizowana później przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="425e3-750">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="425e3-751">`EnsureCreated` jest wywoływana podczas uruchamiania aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="425e3-751">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="425e3-752">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-752">Delete the DB.</span></span>
* <span data-ttu-id="425e3-753">Zmień schemat bazy danych (na przykład Dodaj `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="425e3-753">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="425e3-754">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="425e3-754">Run the app.</span></span>
* <span data-ttu-id="425e3-755">`EnsureCreated` tworzy bazę danych z `EmailAddress` kolumną.</span><span class="sxs-lookup"><span data-stu-id="425e3-755">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="425e3-756">`EnsureCreated` jest wygodnie wczesny podczas opracowywania, gdy schemat jest szybko zmieniany.</span><span class="sxs-lookup"><span data-stu-id="425e3-756">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="425e3-757">W dalszej części tego samouczka baza danych została usunięta, a migracja jest używana.</span><span class="sxs-lookup"><span data-stu-id="425e3-757">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="425e3-758">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="425e3-758">Test the app</span></span>

<span data-ttu-id="425e3-759">Uruchom aplikację i zaakceptuj cookie zasady.</span><span class="sxs-lookup"><span data-stu-id="425e3-759">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="425e3-760">Ta aplikacja nie przechowuje informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="425e3-760">This app doesn't keep personal information.</span></span> <span data-ttu-id="425e3-761">Informacje o zasadach można znaleźć cookie w witrynie [pomocy technicznej ogólne rozporządzenie O ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="425e3-761">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="425e3-762">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="425e3-762">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="425e3-763">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="425e3-763">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="425e3-764">Sprawdzanie kontekstu bazy danych SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="425e3-764">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="425e3-765">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-765">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="425e3-766">Kontekst danych pochodzi od elementu [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="425e3-766">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="425e3-767">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-767">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="425e3-768">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="425e3-768">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="425e3-769">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="425e3-769">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="425e3-770">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="425e3-770">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="425e3-771">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="425e3-771">In EF Core terminology:</span></span>

* <span data-ttu-id="425e3-772">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-772">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="425e3-773">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-773">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="425e3-774">`DbSet<Enrollment>` i można `DbSet<Course>` je pominąć.</span><span class="sxs-lookup"><span data-stu-id="425e3-774">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="425e3-775">EF Core obejmuje te niejawnie `Student` , ponieważ jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="425e3-775">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="425e3-776">W tym samouczku należy zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="425e3-776">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="425e3-777">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="425e3-777">SQL Server Express LocalDB</span></span>

<span data-ttu-id="425e3-778">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="425e3-778">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="425e3-779">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="425e3-779">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="425e3-780">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="425e3-780">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="425e3-781">Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="425e3-781">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="425e3-782">Dodawanie kodu w celu zainicjowania bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="425e3-782">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="425e3-783">EF Core tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-783">EF Core creates an empty DB.</span></span> <span data-ttu-id="425e3-784">W tej sekcji `Initialize` Metoda jest zapisywana w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="425e3-784">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="425e3-785">W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="425e3-785">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="425e3-786">Uwaga: Poprzedni kod używa `Models` dla przestrzeni nazw ( `namespace ContosoUniversity.Models` ), a nie `Data` .</span><span class="sxs-lookup"><span data-stu-id="425e3-786">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="425e3-787">`Models` jest spójny z kodem generowanym przez program rusztowaer.</span><span class="sxs-lookup"><span data-stu-id="425e3-787">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="425e3-788">Aby uzyskać więcej informacji, zobacz [ten problem z szkieletem usługi GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="425e3-788">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="425e3-789">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="425e3-789">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="425e3-790">Jeśli w bazie danych nie ma studentów, baza danych zostanie zainicjowana z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="425e3-790">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="425e3-791">Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="425e3-791">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="425e3-792">`EnsureCreated`Metoda automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-792">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="425e3-793">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-793">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="425e3-794">W *program.cs*Zmień metodę, `Main` Aby wywołać `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="425e3-794">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="425e3-795">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425e3-795">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425e3-796">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="425e3-796">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="425e3-797">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="425e3-797">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="425e3-798">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="425e3-798">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="425e3-799">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="425e3-799">View the DB</span></span>

<span data-ttu-id="425e3-800">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="425e3-800">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="425e3-801">W rezultacie nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="425e3-801">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="425e3-802">Identyfikator GUID będzie różny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="425e3-802">The GUID will be different for each user.</span></span>
<span data-ttu-id="425e3-803">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="425e3-803">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="425e3-804">W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="425e3-804">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="425e3-805">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="425e3-805">Expand the **Tables** node.</span></span>

<span data-ttu-id="425e3-806">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="425e3-806">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="425e3-807">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="425e3-807">Asynchronous code</span></span>

<span data-ttu-id="425e3-808">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="425e3-808">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="425e3-809">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="425e3-809">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="425e3-810">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="425e3-810">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="425e3-811">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="425e3-811">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="425e3-812">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="425e3-812">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="425e3-813">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="425e3-813">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="425e3-814">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="425e3-814">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="425e3-815">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="425e3-815">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="425e3-816">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-816">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="425e3-817">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="425e3-817">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="425e3-818">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="425e3-818">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="425e3-819">Automatycznie Utwórz obiekt [zadania](/dotnet/api/system.threading.tasks.task) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="425e3-819">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="425e3-820">Aby uzyskać więcej informacji, zobacz [Typ zwracany zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="425e3-820">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="425e3-821">Niejawny typ zwracany `Task` reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="425e3-821">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="425e3-822">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="425e3-822">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="425e3-823">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-823">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="425e3-824">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-824">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="425e3-825">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="425e3-825">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="425e3-826">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="425e3-826">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="425e3-827">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="425e3-827">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="425e3-828">Obejmuje, `ToListAsync` ,, `SingleOrDefaultAsync` `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="425e3-828">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="425e3-829">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="425e3-829">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="425e3-830">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="425e3-830">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="425e3-831">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="425e3-831">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="425e3-832">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="425e3-832">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="425e3-833">W następnym samouczku są badane podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="425e3-833">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="425e3-834">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="425e3-834">Additional resources</span></span>

* [<span data-ttu-id="425e3-835">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="425e3-835">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="425e3-836">Dalej</span><span class="sxs-lookup"><span data-stu-id="425e3-836">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end