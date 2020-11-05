---
title: ':::no-loc(Razor)::: Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8'
author: rick-anderson
description: 'Pokazuje, jak utworzyć :::no-loc(Razor)::: aplikację stron przy użyciu Entity Framework Core'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365421"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="83ef1-103">:::no-loc(Razor)::: Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8</span><span class="sxs-lookup"><span data-stu-id="83ef1-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="83ef1-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83ef1-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="83ef1-105">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="83ef1-106">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="83ef1-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="83ef1-107">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="83ef1-108">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="83ef1-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="83ef1-109">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="83ef1-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="83ef1-110">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="83ef1-111">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="83ef1-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="83ef1-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="83ef1-112">Prerequisites</span></span>

* <span data-ttu-id="83ef1-113">Jeśli :::no-loc(Razor)::: dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do :::no-loc(Razor)::: stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="83ef1-116">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-116">Database engines</span></span>

<span data-ttu-id="83ef1-117">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="83ef1-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="83ef1-118">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="83ef1-119">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="83ef1-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="83ef1-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="83ef1-120">Troubleshooting</span></span>

<span data-ttu-id="83ef1-121">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="83ef1-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="83ef1-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="83ef1-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="83ef1-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="83ef1-123">The sample app</span></span>

<span data-ttu-id="83ef1-124">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="83ef1-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="83ef1-125">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="83ef1-126">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="83ef1-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="83ef1-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="83ef1-130">Fokus samouczka polega na tym, jak używać EF Core z ASP.NET Core, a nie sposobu dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83ef1-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="83ef1-131">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="83ef1-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-133">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="83ef1-134">Wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="83ef1-135">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="83ef1-136">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="83ef1-137">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-137">Select **Create**.</span></span>
* <span data-ttu-id="83ef1-138">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 5,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-140">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="83ef1-141">Uruchom następujące polecenia, aby utworzyć :::no-loc(Razor)::: Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-141">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="83ef1-142">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="83ef1-142">Set up the site style</span></span>

<span data-ttu-id="83ef1-143">Skopiuj i wklej następujący kod do pliku *Pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="83ef1-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="83ef1-144">Plik układu ustawia nagłówek, stopkę i menu witryny.</span><span class="sxs-lookup"><span data-stu-id="83ef1-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="83ef1-145">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="83ef1-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="83ef1-146">Każde wystąpienie "ContosoUniversity" na "University-Uniwersytet".</span><span class="sxs-lookup"><span data-stu-id="83ef1-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="83ef1-147">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-147">There are three occurrences.</span></span>
* <span data-ttu-id="83ef1-148">Wpisy menu **Home** i **privacy** są usuwane.</span><span class="sxs-lookup"><span data-stu-id="83ef1-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="83ef1-149">Wpisy są **dodawane do programu** , **studentów** , **kursów** , **instruktorów** i **działów**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="83ef1-150">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="83ef1-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="83ef1-151">Poprzedni kod zastępuje tekst dotyczący ASP.NET Core tekstem dla tej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="83ef1-152">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="83ef1-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="83ef1-153">Model danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-153">The data model</span></span>

<span data-ttu-id="83ef1-154">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="83ef1-154">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="83ef1-156">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="83ef1-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="83ef1-157">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="83ef1-157">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="83ef1-159">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="83ef1-160">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="83ef1-161">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="83ef1-162">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="83ef1-163">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="83ef1-164">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="83ef1-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="83ef1-165">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="83ef1-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="83ef1-166">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="83ef1-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="83ef1-167">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="83ef1-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="83ef1-168">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="83ef1-169">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="83ef1-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="83ef1-170">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="83ef1-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="83ef1-171">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="83ef1-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="83ef1-172">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="83ef1-173">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="83ef1-174">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="83ef1-175">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="83ef1-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="83ef1-176">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="83ef1-176">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="83ef1-178">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="83ef1-179">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="83ef1-180">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="83ef1-181">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="83ef1-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="83ef1-182">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="83ef1-183">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="83ef1-184">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="83ef1-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="83ef1-185">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="83ef1-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="83ef1-186">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="83ef1-187">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="83ef1-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="83ef1-188">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="83ef1-189">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="83ef1-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="83ef1-190">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="83ef1-191">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="83ef1-192">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="83ef1-193">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="83ef1-194">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="83ef1-194">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="83ef1-196">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="83ef1-197">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="83ef1-198">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="83ef1-199">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="83ef1-200">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="83ef1-201">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="83ef1-201">Scaffold Student pages</span></span>

<span data-ttu-id="83ef1-202">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="83ef1-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="83ef1-203">Klasa EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="83ef1-204">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="83ef1-205">Pochodzi od <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="83ef1-206">:::no-loc(Razor)::: strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="83ef1-206">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-208">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="83ef1-209">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="83ef1-210">W oknie dialogowym **Dodaj nowy element szkieletowy** :</span><span class="sxs-lookup"><span data-stu-id="83ef1-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="83ef1-211">Na lewej karcie Wybierz pozycję **zainstalowane > typowe :::no-loc(Razor)::: strony >**</span><span class="sxs-lookup"><span data-stu-id="83ef1-211">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="83ef1-212">Wybierz **:::no-loc(Razor)::: strony przy użyciu Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-212">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="83ef1-213">Na **stronie Dodawanie :::no-loc(Razor)::: stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="83ef1-213">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="83ef1-214">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="83ef1-215">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="83ef1-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="83ef1-216">Zmień nazwę kontekstu danych na End `SchoolContext` zamiast `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="83ef1-217">Nazwa zaktualizowanego kontekstu: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="83ef1-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="83ef1-218">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-218">Select **Add**.</span></span>

<span data-ttu-id="83ef1-219">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="83ef1-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-221">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="83ef1-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="83ef1-222">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="83ef1-223">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="83ef1-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="83ef1-224">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="83ef1-225">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="83ef1-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="83ef1-226">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="83ef1-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="83ef1-227">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="83ef1-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="83ef1-228">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="83ef1-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="83ef1-229">Jeśli poprzedni krok zakończy się niepowodzeniem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="83ef1-230">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-230">The scaffolding process:</span></span>

* <span data-ttu-id="83ef1-231">Tworzy :::no-loc(Razor)::: strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="83ef1-231">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="83ef1-232">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-233">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-234">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-235">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-236">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="83ef1-237">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="83ef1-238">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="83ef1-239">Dodaje parametry połączenia z bazą danych do programu *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-239">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="83ef1-240">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-240">Database connection string</span></span>

<span data-ttu-id="83ef1-241">Narzędzie do tworzenia szkieletu generuje parametry połączenia w *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="83ef1-241">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83ef1-243">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="83ef1-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="83ef1-244">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="83ef1-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="83ef1-245">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83ef1-247">Skróć parametry połączenia oprogramowania SQLite do *bazy danych cu. DB* :</span><span class="sxs-lookup"><span data-stu-id="83ef1-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="83ef1-248">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-248">Update the database context class</span></span>

<span data-ttu-id="83ef1-249">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="83ef1-250">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="83ef1-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="83ef1-251">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="83ef1-252">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="83ef1-253">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="83ef1-254">Poprzedni kod zmienia się z pojedynczej `DbSet<Student> Student` na plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="83ef1-255">Aby :::no-loc(Razor)::: kod stron był zgodny z nową `DBSet` nazwą, wprowadź globalną zmianę z: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="83ef1-255">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="83ef1-256">do `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="83ef1-256">to: `_context.Students.`</span></span>

<span data-ttu-id="83ef1-257">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="83ef1-257">There are 8 occurrences.</span></span>

<span data-ttu-id="83ef1-258">Ponieważ zestaw jednostek zawiera wiele jednostek, wielu deweloperów preferuje `DBSet` nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="83ef1-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="83ef1-259">Wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="83ef1-259">The highlighted code:</span></span>

* <span data-ttu-id="83ef1-260">Tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="83ef1-261">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="83ef1-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="83ef1-262">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="83ef1-263">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="83ef1-264">Wywołania <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="83ef1-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="83ef1-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="83ef1-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="83ef1-266">Jest wywoływana, gdy została `SchoolContext` zainicjowana, ale zanim model zostanie zablokowany i użyty do zainicjowania kontekstu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="83ef1-267">Jest wymagany, ponieważ w dalszej części samouczka `Student` jednostka będzie miała odwołania do innych jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="83ef1-268">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="83ef1-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="83ef1-269">Startup.cs</span></span>

<span data-ttu-id="83ef1-270">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="83ef1-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="83ef1-271">Usługi takie jak `SchoolContext` są rejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="83ef1-272">Składniki, które wymagają tych usług, takie jak :::no-loc(Razor)::: strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-272">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="83ef1-273">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="83ef1-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="83ef1-274">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="83ef1-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83ef1-276">Program szkieleter dodał następujące wyróżnione wiersze:</span><span class="sxs-lookup"><span data-stu-id="83ef1-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83ef1-278">Sprawdź kod dodany przez wywołania szkieletu `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="83ef1-279">Aby uzyskać informacje na temat korzystania z produkcyjnej bazy danych, zobacz temat [Korzystanie z oprogramowania SQLite na potrzeby programowania. SQL Server do produkcji](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="83ef1-280">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="83ef1-281">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="83ef1-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="83ef1-282">Dodaj filtr wyjątku bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-282">Add the database exception filter</span></span>

<span data-ttu-id="83ef1-283">Dodaj <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> do `ConfigureServices` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="83ef1-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="83ef1-285">Dodaj pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="83ef1-286">W obszarze PMC wprowadź następujące polecenie, aby dodać pakiet NuGet:</span><span class="sxs-lookup"><span data-stu-id="83ef1-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="83ef1-288">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`Pakiet NuGet zawiera ASP.NET Core oprogramowanie pośredniczące dla Entity Framework Core stron błędów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="83ef1-289">To oprogramowanie pośredniczące pomaga wykrywać i diagnozować błędy za pomocą migracji Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="83ef1-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="83ef1-290">`AddDatabaseDeveloperPageExceptionFilter`Zapewnia przydatne informacje o błędzie w [środowisku programistycznym](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="83ef1-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="83ef1-291">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-291">Create the database</span></span>

<span data-ttu-id="83ef1-292">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="83ef1-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="83ef1-293">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="83ef1-294">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="83ef1-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="83ef1-295">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="83ef1-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="83ef1-296">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-296">Delete the database.</span></span> <span data-ttu-id="83ef1-297">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="83ef1-297">Any existing data is lost.</span></span>
* <span data-ttu-id="83ef1-298">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-298">Change the data model.</span></span> <span data-ttu-id="83ef1-299">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="83ef1-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="83ef1-300">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-300">Run the app.</span></span>
* <span data-ttu-id="83ef1-301">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="83ef1-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="83ef1-302">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="83ef1-303">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="83ef1-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="83ef1-304">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="83ef1-305">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="83ef1-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="83ef1-306">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="83ef1-307">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="83ef1-307">Test the app</span></span>

* <span data-ttu-id="83ef1-308">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-308">Run the app.</span></span>
* <span data-ttu-id="83ef1-309">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="83ef1-310">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="83ef1-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="83ef1-311">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-311">Seed the database</span></span>

<span data-ttu-id="83ef1-312">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="83ef1-313">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="83ef1-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="83ef1-314">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="83ef1-315">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="83ef1-316">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="83ef1-317">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="83ef1-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="83ef1-318">W *program.cs* Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="83ef1-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83ef1-320">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="83ef1-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="83ef1-321">Odpowiedz przy użyciu `Y` , aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-323">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="83ef1-324">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-324">Restart the app.</span></span>
* <span data-ttu-id="83ef1-325">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="83ef1-326">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-328">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="83ef1-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="83ef1-329">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="83ef1-330">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="83ef1-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="83ef1-331">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="83ef1-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="83ef1-332">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="83ef1-333">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83ef1-335">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="83ef1-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="83ef1-336">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="83ef1-337">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="83ef1-337">Asynchronous code</span></span>

<span data-ttu-id="83ef1-338">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="83ef1-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="83ef1-339">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="83ef1-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="83ef1-340">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="83ef1-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="83ef1-341">W przypadku kodu synchronicznego wiele wątków może zostać rozwiązanych, chociaż nie działają, ponieważ oczekują na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="83ef1-342">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="83ef1-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="83ef1-343">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="83ef1-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="83ef1-344">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="83ef1-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="83ef1-345">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="83ef1-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="83ef1-346">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="83ef1-347">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="83ef1-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="83ef1-348">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="83ef1-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="83ef1-349">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="83ef1-350">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="83ef1-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="83ef1-351">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="83ef1-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="83ef1-352">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="83ef1-353">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="83ef1-354">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="83ef1-355">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="83ef1-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="83ef1-356">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="83ef1-357">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="83ef1-358">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="83ef1-359">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="83ef1-360">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="83ef1-361">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="83ef1-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="83ef1-362">Zagadnienia dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="83ef1-362">Performance considerations</span></span>

<span data-ttu-id="83ef1-363">Ogólnie rzecz biorąc, Strona sieci Web nie powinna ładować dowolnej liczby wierszy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="83ef1-364">Zapytanie powinno używać podejścia do ograniczania.</span><span class="sxs-lookup"><span data-stu-id="83ef1-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="83ef1-365">Na przykład poprzednie zapytanie może użyć, `Take` Aby ograniczyć liczbę zwracanych wierszy:</span><span class="sxs-lookup"><span data-stu-id="83ef1-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="83ef1-366">Wyliczenie dużej tabeli w widoku może zwrócić częściowo skonstruowaną odpowiedź HTTP 200, jeśli wyjątek bazy danych wystąpił częściowo przez wyliczenie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="83ef1-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> wartość domyślna to 1024.</span><span class="sxs-lookup"><span data-stu-id="83ef1-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="83ef1-368">Następujące zestawy kodu `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="83ef1-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="83ef1-369">Stronicowanie jest omówione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="83ef1-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="83ef1-370">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="83ef1-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="83ef1-371">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="83ef1-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="83ef1-372">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="83ef1-373">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="83ef1-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="83ef1-374">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="83ef1-375">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="83ef1-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="83ef1-376">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="83ef1-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="83ef1-377">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="83ef1-378">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="83ef1-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="83ef1-379">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="83ef1-379">Prerequisites</span></span>

* <span data-ttu-id="83ef1-380">Jeśli :::no-loc(Razor)::: dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do :::no-loc(Razor)::: stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-380">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="83ef1-383">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-383">Database engines</span></span>

<span data-ttu-id="83ef1-384">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="83ef1-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="83ef1-385">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="83ef1-386">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="83ef1-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="83ef1-387">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="83ef1-387">Troubleshooting</span></span>

<span data-ttu-id="83ef1-388">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="83ef1-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="83ef1-389">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="83ef1-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="83ef1-390">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="83ef1-390">The sample app</span></span>

<span data-ttu-id="83ef1-391">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="83ef1-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="83ef1-392">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="83ef1-393">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="83ef1-393">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="83ef1-396">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="83ef1-397">Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83ef1-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="83ef1-398">Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="83ef1-399">Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="83ef1-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="83ef1-400">Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83ef1-402">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="83ef1-403">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="83ef1-403">Build the project.</span></span>
* <span data-ttu-id="83ef1-404">W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="83ef1-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="83ef1-405">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83ef1-407">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="83ef1-408">Usuń *ContosoUniversity. csproj* i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="83ef1-409">W *program.cs* , komentarz `#define Startup` `StartupSQLite` jest używany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="83ef1-410">Usuń *appSettings.js* i zmień nazwę *appSettingsSQLite.jsna* na *appSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="83ef1-411">Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="83ef1-412">Wykonaj wyszukiwanie globalne dla `#if SQLiteVersion` i Usuń `#if SQLiteVersion` oraz skojarzoną `#endif` instrukcję.</span><span class="sxs-lookup"><span data-stu-id="83ef1-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="83ef1-413">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="83ef1-413">Build the project.</span></span>
* <span data-ttu-id="83ef1-414">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="83ef1-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="83ef1-415">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="83ef1-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="83ef1-416">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="83ef1-417">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="83ef1-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-419">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="83ef1-420">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="83ef1-421">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="83ef1-422">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="83ef1-423">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-425">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="83ef1-426">Uruchom następujące polecenia, aby utworzyć :::no-loc(Razor)::: Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-426">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="83ef1-427">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="83ef1-427">Set up the site style</span></span>

<span data-ttu-id="83ef1-428">Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="83ef1-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="83ef1-429">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="83ef1-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="83ef1-430">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-430">There are three occurrences.</span></span>

* <span data-ttu-id="83ef1-431">Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów** , **kursów** , **instruktorów** i **działów**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="83ef1-432">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="83ef1-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="83ef1-433">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="83ef1-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="83ef1-434">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="83ef1-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="83ef1-435">Model danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-435">The data model</span></span>

<span data-ttu-id="83ef1-436">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="83ef1-436">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="83ef1-438">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="83ef1-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="83ef1-439">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="83ef1-439">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="83ef1-441">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="83ef1-442">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="83ef1-443">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="83ef1-444">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="83ef1-445">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="83ef1-446">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="83ef1-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="83ef1-447">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="83ef1-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="83ef1-448">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="83ef1-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="83ef1-449">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="83ef1-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="83ef1-450">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="83ef1-451">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="83ef1-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="83ef1-452">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="83ef1-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="83ef1-453">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="83ef1-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="83ef1-454">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="83ef1-455">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="83ef1-456">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="83ef1-457">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="83ef1-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="83ef1-458">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="83ef1-458">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="83ef1-460">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="83ef1-461">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="83ef1-462">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="83ef1-463">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="83ef1-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="83ef1-464">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="83ef1-465">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="83ef1-466">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="83ef1-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="83ef1-467">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="83ef1-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="83ef1-468">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="83ef1-469">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="83ef1-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="83ef1-470">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="83ef1-471">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="83ef1-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="83ef1-472">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="83ef1-473">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="83ef1-474">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="83ef1-475">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="83ef1-476">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="83ef1-476">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="83ef1-478">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="83ef1-479">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="83ef1-480">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="83ef1-481">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="83ef1-482">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="83ef1-483">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="83ef1-483">Scaffold Student pages</span></span>

<span data-ttu-id="83ef1-484">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="83ef1-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="83ef1-485">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="83ef1-485">An EF Core *context* class.</span></span> <span data-ttu-id="83ef1-486">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="83ef1-487">Pochodzi od `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="83ef1-488">:::no-loc(Razor)::: strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="83ef1-488">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-490">Utwórz folder *uczniów* w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="83ef1-491">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="83ef1-492">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **:::no-loc(Razor)::: strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-492">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="83ef1-493">Na **stronie Dodawanie :::no-loc(Razor)::: stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="83ef1-493">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="83ef1-494">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="83ef1-495">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="83ef1-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="83ef1-496">Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="83ef1-497">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-497">Select **Add**.</span></span>

<span data-ttu-id="83ef1-498">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="83ef1-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-500">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="83ef1-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="83ef1-501">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="83ef1-502">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="83ef1-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="83ef1-503">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="83ef1-504">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="83ef1-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="83ef1-505">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="83ef1-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="83ef1-506">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="83ef1-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="83ef1-507">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="83ef1-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="83ef1-508">Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="83ef1-509">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-509">The scaffolding process:</span></span>

* <span data-ttu-id="83ef1-510">Tworzy :::no-loc(Razor)::: strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="83ef1-510">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="83ef1-511">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-512">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-513">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-514">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="83ef1-515">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="83ef1-516">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="83ef1-517">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="83ef1-518">Dodaje parametry połączenia z bazą danych do programu *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-518">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="83ef1-519">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83ef1-521">*:::no-loc(appsettings.json):::* Plik określa parametry połączenia [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="83ef1-521">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="83ef1-522">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="83ef1-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="83ef1-523">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83ef1-525">Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB* :</span><span class="sxs-lookup"><span data-stu-id="83ef1-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="83ef1-526">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-526">Update the database context class</span></span>

<span data-ttu-id="83ef1-527">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="83ef1-528">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="83ef1-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="83ef1-529">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="83ef1-530">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="83ef1-531">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="83ef1-532">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="83ef1-533">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="83ef1-533">In EF Core terminology:</span></span>

* <span data-ttu-id="83ef1-534">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="83ef1-535">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="83ef1-536">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="83ef1-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="83ef1-537">Ponieważ narzędzie tworzenia szkieletów utworzyło `Student` nieogólnymi, ten krok zmienia go na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="83ef1-538">Aby :::no-loc(Razor)::: kod stron był zgodny z nową nazwą nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-538">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="83ef1-539">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="83ef1-539">There are 8 occurrences.</span></span>

<span data-ttu-id="83ef1-540">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="83ef1-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="83ef1-541">Startup.cs</span></span>

<span data-ttu-id="83ef1-542">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="83ef1-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="83ef1-543">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="83ef1-544">Składniki wymagające tych usług (takie jak :::no-loc(Razor)::: strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-544">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="83ef1-545">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="83ef1-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="83ef1-546">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="83ef1-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-548">W programie `ConfigureServices` wyróżnione wiersze zostały dodane przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-550">W programie upewnij `ConfigureServices` się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="83ef1-551">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="83ef1-552">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="83ef1-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="83ef1-553">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-553">Create the database</span></span>

<span data-ttu-id="83ef1-554">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="83ef1-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="83ef1-555">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="83ef1-556">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="83ef1-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="83ef1-557">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="83ef1-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="83ef1-558">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-558">Delete the database.</span></span> <span data-ttu-id="83ef1-559">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="83ef1-559">Any existing data is lost.</span></span>
* <span data-ttu-id="83ef1-560">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-560">Change the data model.</span></span> <span data-ttu-id="83ef1-561">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="83ef1-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="83ef1-562">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-562">Run the app.</span></span>
* <span data-ttu-id="83ef1-563">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="83ef1-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="83ef1-564">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="83ef1-565">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="83ef1-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="83ef1-566">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="83ef1-567">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="83ef1-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="83ef1-568">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="83ef1-569">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="83ef1-569">Test the app</span></span>

* <span data-ttu-id="83ef1-570">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-570">Run the app.</span></span>
* <span data-ttu-id="83ef1-571">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="83ef1-572">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="83ef1-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="83ef1-573">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-573">Seed the database</span></span>

<span data-ttu-id="83ef1-574">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="83ef1-575">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="83ef1-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="83ef1-576">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="83ef1-577">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="83ef1-578">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="83ef1-579">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="83ef1-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="83ef1-580">W *program.cs* Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="83ef1-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83ef1-582">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="83ef1-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-584">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="83ef1-585">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-585">Restart the app.</span></span>

* <span data-ttu-id="83ef1-586">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="83ef1-587">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-589">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="83ef1-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="83ef1-590">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="83ef1-591">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="83ef1-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="83ef1-592">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="83ef1-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="83ef1-593">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="83ef1-594">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83ef1-596">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="83ef1-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="83ef1-597">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="83ef1-598">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="83ef1-598">Asynchronous code</span></span>

<span data-ttu-id="83ef1-599">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="83ef1-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="83ef1-600">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="83ef1-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="83ef1-601">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="83ef1-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="83ef1-602">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="83ef1-603">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="83ef1-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="83ef1-604">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="83ef1-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="83ef1-605">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="83ef1-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="83ef1-606">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="83ef1-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="83ef1-607">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="83ef1-608">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="83ef1-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="83ef1-609">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="83ef1-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="83ef1-610">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="83ef1-611">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="83ef1-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="83ef1-612">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="83ef1-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="83ef1-613">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="83ef1-614">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="83ef1-615">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="83ef1-616">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="83ef1-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="83ef1-617">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="83ef1-618">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="83ef1-619">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="83ef1-620">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="83ef1-621">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="83ef1-622">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="83ef1-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="83ef1-623">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="83ef1-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="83ef1-624">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="83ef1-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83ef1-625">Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji ASP.NET Core :::no-loc(Razor)::: stronach przy użyciu programu Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="83ef1-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="83ef1-626">Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="83ef1-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="83ef1-627">Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="83ef1-628">Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają sposób tworzenia przykładowej aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="83ef1-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="83ef1-629">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="83ef1-630">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="83ef1-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="83ef1-631">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="83ef1-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="83ef1-634">Znajomość [ :::no-loc(Razor)::: stron](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="83ef1-634">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="83ef1-635">Nowi programiści powinni zakończyć pracę [z :::no-loc(Razor)::: stronami](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="83ef1-635">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="83ef1-636">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="83ef1-636">Troubleshooting</span></span>

<span data-ttu-id="83ef1-637">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="83ef1-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="83ef1-638">Dobrym sposobem uzyskania pomocy jest zaksięgowanie pytania [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) na [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="83ef1-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="83ef1-639">Aplikacja sieci Web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="83ef1-639">The Contoso University web app</span></span>

<span data-ttu-id="83ef1-640">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="83ef1-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="83ef1-641">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="83ef1-642">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="83ef1-642">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

<span data-ttu-id="83ef1-645">Styl interfejsu użytkownika tej witryny jest zbliżony do zawartości wygenerowanej przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="83ef1-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="83ef1-646">Fokus samouczka znajduje się na EF Core ze :::no-loc(Razor)::: stronami, a nie interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83ef1-646">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="83ef1-647">Tworzenie :::no-loc(Razor)::: aplikacji sieci Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="83ef1-647">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-649">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="83ef1-650">Utwórz nową aplikację sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83ef1-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="83ef1-651">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="83ef1-652">Ważne jest, aby nazwa projektu *ContosoUniversity* , tak aby przestrzenie nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="83ef1-653">Wybierz pozycję **ASP.NET Core 2,1** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="83ef1-654">Aby poznać obrazy powyższych kroków, zobacz [Tworzenie :::no-loc(Razor)::: aplikacji sieci Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="83ef1-654">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="83ef1-655">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="83ef1-657">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="83ef1-657">Set up the site style</span></span>

<span data-ttu-id="83ef1-658">Kilka zmian powoduje skonfigurowanie menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="83ef1-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="83ef1-659">Aktualizowanie *stron/Shared/_Layout. cshtml* z następującymi zmianami:</span><span class="sxs-lookup"><span data-stu-id="83ef1-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="83ef1-660">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="83ef1-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="83ef1-661">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-661">There are three occurrences.</span></span>

* <span data-ttu-id="83ef1-662">Dodaj pozycje menu dla **studentów** , **kursów** , **instruktorów** i **działów** , a następnie usuń wpis menu **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="83ef1-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="83ef1-663">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="83ef1-663">The changes are highlighted.</span></span> <span data-ttu-id="83ef1-664">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="83ef1-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="83ef1-665">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="83ef1-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="83ef1-666">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-666">Create the data model</span></span>

<span data-ttu-id="83ef1-667">Utwórz klasy jednostek dla aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="83ef1-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="83ef1-668">Zacznij od następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="83ef1-668">Start with the following three entities:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="83ef1-670">Istnieje relacja jeden do wielu między elementami `Student` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="83ef1-671">Istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="83ef1-672">Student może zarejestrować się w dowolnej liczbie kursów.</span><span class="sxs-lookup"><span data-stu-id="83ef1-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="83ef1-673">Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.</span><span class="sxs-lookup"><span data-stu-id="83ef1-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="83ef1-674">W poniższych sekcjach zostanie utworzona Klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="83ef1-675">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="83ef1-675">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

<span data-ttu-id="83ef1-677">Utwórz folder *models* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-677">Create a *Models* folder.</span></span> <span data-ttu-id="83ef1-678">W folderze *modele* Utwórz plik klasy o nazwie *student.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="83ef1-679">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych (DB), która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="83ef1-680">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="83ef1-681">W `classnameID` , `classname` jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="83ef1-682">Alternatywny automatycznie rozpoznany klucz podstawowy znajduje się `StudentID` w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="83ef1-683">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="83ef1-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="83ef1-684">Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="83ef1-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="83ef1-685">W tym przypadku `Enrollments` Właściwość a `Student entity` zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym `Student` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="83ef1-686">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="83ef1-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="83ef1-687">Powiązany `Enrollment` wiersz jest wierszem zawierającym wartość klucza podstawowego studenta w `StudentID` kolumnie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="83ef1-688">Załóżmy na przykład, że student o IDENTYFIKATORze 1 ma dwa wiersze w `Enrollment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="83ef1-689">`Enrollment`Tabela ma dwa wiersze z `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="83ef1-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="83ef1-690">`StudentID` jest kluczem obcym w `Enrollment` tabeli, który określa student w `Student` tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="83ef1-691">Jeśli właściwość nawigacji może zawierać wiele jednostek, właściwość nawigacji musi być typem listy, na przykład `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="83ef1-692">`ICollection<T>` można określić typ, taki jak `List<T>` lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="83ef1-693">Gdy `ICollection<T>` jest używany, EF Core domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="83ef1-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="83ef1-694">Właściwości nawigacji, które przechowują wiele jednostek, pochodzą z relacji "wiele do wielu" i "jeden do wielu".</span><span class="sxs-lookup"><span data-stu-id="83ef1-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="83ef1-695">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="83ef1-695">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="83ef1-697">W folderze *modele* Utwórz *Enrollment.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="83ef1-698">`EnrollmentID`Właściwość jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="83ef1-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="83ef1-699">Ta jednostka używa `classnameID` wzorca zamiast `ID` `Student` obiektu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="83ef1-700">Zazwyczaj deweloperzy wybierają jeden wzorzec i używają go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="83ef1-701">W późniejszym samouczku, użycie identyfikatora bez ClassName jest pokazane, aby ułatwić implementację dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="83ef1-702">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="83ef1-703">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null.</span><span class="sxs-lookup"><span data-stu-id="83ef1-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="83ef1-704">Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="83ef1-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="83ef1-705">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="83ef1-706">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="83ef1-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="83ef1-707">`Student`Jednostka różni się od `Student.Enrollments` właściwości nawigacji, która zawiera wiele `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="83ef1-708">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="83ef1-709">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="83ef1-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="83ef1-710">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="83ef1-711">Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="83ef1-712">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="83ef1-713">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="83ef1-714">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="83ef1-714">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="83ef1-716">W folderze *modele* Utwórz *Course.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="83ef1-717">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="83ef1-718">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="83ef1-719">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast generowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="83ef1-720">Tworzenie szkieletu modelu ucznia</span><span class="sxs-lookup"><span data-stu-id="83ef1-720">Scaffold the student model</span></span>

<span data-ttu-id="83ef1-721">W tej sekcji model ucznia jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="83ef1-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="83ef1-722">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="83ef1-723">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="83ef1-723">Build the project.</span></span>
* <span data-ttu-id="83ef1-724">Utwórz folder *strony/uczniowie* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83ef1-726">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="83ef1-727">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **:::no-loc(Razor)::: strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-727">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="83ef1-728">Wypełnij okno dialogowe **Dodawanie :::no-loc(Razor)::: stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="83ef1-728">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="83ef1-729">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="83ef1-730">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę na **ContosoUniversity. models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="83ef1-731">Z listy rozwijanej **Klasa kontekstu danych** wybierz pozycję **ContosoUniversity. models. SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="83ef1-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="83ef1-732">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-732">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="83ef1-734">Zobacz Tworzenie [szkieletu modelu filmu w](xref:tutorials/razor-pages/model#scaffold-the-movie-model) przypadku problemu z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="83ef1-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83ef1-736">Uruchom następujące polecenia, aby uzyskać szkielet modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="83ef1-737">Proces szkieletu utworzył i zmienił następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="83ef1-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="83ef1-738">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="83ef1-738">Files created</span></span>

* <span data-ttu-id="83ef1-739">*Strony/uczniowie* Tworzenie, usuwanie, szczegóły, edytowanie, indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="83ef1-740">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="83ef1-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="83ef1-741">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="83ef1-741">File updates</span></span>

* <span data-ttu-id="83ef1-742">*Startup.cs* : szczegółowe zmiany w tym pliku opisano w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="83ef1-743">*:::no-loc(appsettings.json):::* : Dodano parametry połączenia używane do nawiązania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-743">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="83ef1-744">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="83ef1-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="83ef1-745">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="83ef1-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="83ef1-746">Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="83ef1-747">Składniki wymagające tych usług (takie jak :::no-loc(Razor)::: strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="83ef1-747">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="83ef1-748">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="83ef1-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="83ef1-749">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="83ef1-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="83ef1-750">Zapoznaj się z `ConfigureServices` metodą w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="83ef1-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="83ef1-751">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="83ef1-752">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="83ef1-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="83ef1-753">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="83ef1-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="83ef1-754">Aktualizuj główne</span><span class="sxs-lookup"><span data-stu-id="83ef1-754">Update main</span></span>

<span data-ttu-id="83ef1-755">W *program.cs* Zmień metodę, `Main` Aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="83ef1-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="83ef1-756">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="83ef1-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="83ef1-757">Wywołaj  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="83ef1-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="83ef1-758">Usuń kontekst, gdy `EnsureCreated` Metoda zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="83ef1-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="83ef1-759">Poniższy kod przedstawia zaktualizowany plik *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="83ef1-760">`EnsureCreated` zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="83ef1-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="83ef1-761">Jeśli istnieje, nie zostanie podjęta żadna akcja.</span><span class="sxs-lookup"><span data-stu-id="83ef1-761">If it exists, no action is taken.</span></span> <span data-ttu-id="83ef1-762">Jeśli nie istnieje, zostanie utworzona baza danych i jej wszystkie schematy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="83ef1-763">`EnsureCreated` Program nie korzysta z migracji w celu utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="83ef1-764">Baza danych utworzona za pomocą programu `EnsureCreated` nie może zostać zaktualizowana później przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="83ef1-765">`EnsureCreated` jest wywoływana podczas uruchamiania aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="83ef1-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="83ef1-766">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-766">Delete the DB.</span></span>
* <span data-ttu-id="83ef1-767">Zmień schemat bazy danych (na przykład Dodaj `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="83ef1-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="83ef1-768">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="83ef1-768">Run the app.</span></span>
* <span data-ttu-id="83ef1-769">`EnsureCreated` tworzy bazę danych z `EmailAddress` kolumną.</span><span class="sxs-lookup"><span data-stu-id="83ef1-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="83ef1-770">`EnsureCreated` jest wygodnie wczesny podczas opracowywania, gdy schemat jest szybko zmieniany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="83ef1-771">W dalszej części tego samouczka baza danych została usunięta, a migracja jest używana.</span><span class="sxs-lookup"><span data-stu-id="83ef1-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="83ef1-772">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="83ef1-772">Test the app</span></span>

<span data-ttu-id="83ef1-773">Uruchom aplikację i zaakceptuj :::no-loc(cookie)::: zasady.</span><span class="sxs-lookup"><span data-stu-id="83ef1-773">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="83ef1-774">Ta aplikacja nie przechowuje informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="83ef1-775">Informacje o zasadach można znaleźć :::no-loc(cookie)::: w witrynie [pomocy technicznej ogólne rozporządzenie O ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="83ef1-775">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="83ef1-776">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="83ef1-777">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="83ef1-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="83ef1-778">Sprawdzanie kontekstu bazy danych SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="83ef1-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="83ef1-779">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="83ef1-780">Kontekst danych pochodzi od elementu [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="83ef1-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="83ef1-781">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="83ef1-782">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="83ef1-783">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="83ef1-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="83ef1-784">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="83ef1-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="83ef1-785">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="83ef1-785">In EF Core terminology:</span></span>

* <span data-ttu-id="83ef1-786">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="83ef1-787">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="83ef1-788">`DbSet<Enrollment>` i można `DbSet<Course>` je pominąć.</span><span class="sxs-lookup"><span data-stu-id="83ef1-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="83ef1-789">EF Core obejmuje te niejawnie `Student` , ponieważ jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="83ef1-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="83ef1-790">W tym samouczku należy zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="83ef1-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="83ef1-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="83ef1-792">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="83ef1-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="83ef1-793">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="83ef1-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="83ef1-794">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="83ef1-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="83ef1-795">Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="83ef1-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="83ef1-796">Dodawanie kodu w celu zainicjowania bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="83ef1-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="83ef1-797">EF Core tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="83ef1-798">W tej sekcji `Initialize` Metoda jest zapisywana w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="83ef1-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="83ef1-799">W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="83ef1-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="83ef1-800">Uwaga: Poprzedni kod używa `Models` dla przestrzeni nazw ( `namespace ContosoUniversity.Models` ), a nie `Data` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="83ef1-801">`Models` jest spójny z kodem generowanym przez program rusztowaer.</span><span class="sxs-lookup"><span data-stu-id="83ef1-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="83ef1-802">Aby uzyskać więcej informacji, zobacz [ten problem z szkieletem usługi GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="83ef1-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="83ef1-803">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="83ef1-804">Jeśli w bazie danych nie ma studentów, baza danych zostanie zainicjowana z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="83ef1-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="83ef1-805">Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="83ef1-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="83ef1-806">`EnsureCreated`Metoda automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="83ef1-807">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="83ef1-808">W *program.cs* Zmień metodę, `Main` Aby wywołać `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="83ef1-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="83ef1-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83ef1-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83ef1-810">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="83ef1-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="83ef1-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83ef1-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83ef1-812">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="83ef1-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="83ef1-813">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="83ef1-813">View the DB</span></span>

<span data-ttu-id="83ef1-814">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="83ef1-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="83ef1-815">W rezultacie nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="83ef1-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="83ef1-816">Identyfikator GUID będzie różny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83ef1-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="83ef1-817">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="83ef1-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="83ef1-818">W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="83ef1-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="83ef1-819">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="83ef1-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="83ef1-820">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="83ef1-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="83ef1-821">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="83ef1-821">Asynchronous code</span></span>

<span data-ttu-id="83ef1-822">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="83ef1-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="83ef1-823">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="83ef1-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="83ef1-824">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="83ef1-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="83ef1-825">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="83ef1-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="83ef1-826">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="83ef1-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="83ef1-827">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="83ef1-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="83ef1-828">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="83ef1-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="83ef1-829">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="83ef1-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="83ef1-830">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="83ef1-831">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="83ef1-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="83ef1-832">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="83ef1-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="83ef1-833">Automatycznie Utwórz obiekt [zadania](/dotnet/api/system.threading.tasks.task) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="83ef1-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="83ef1-834">Aby uzyskać więcej informacji, zobacz [Typ zwracany zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="83ef1-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="83ef1-835">Niejawny typ zwracany `Task` reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="83ef1-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="83ef1-836">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="83ef1-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="83ef1-837">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="83ef1-838">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="83ef1-839">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="83ef1-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="83ef1-840">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="83ef1-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="83ef1-841">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="83ef1-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="83ef1-842">Obejmuje, `ToListAsync` ,, `SingleOrDefaultAsync` `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="83ef1-843">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="83ef1-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="83ef1-844">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="83ef1-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="83ef1-845">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="83ef1-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="83ef1-846">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="83ef1-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="83ef1-847">W następnym samouczku są badane podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="83ef1-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="83ef1-848">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="83ef1-848">Additional resources</span></span>

* [<span data-ttu-id="83ef1-849">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="83ef1-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="83ef1-850">Dalej</span><span class="sxs-lookup"><span data-stu-id="83ef1-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
